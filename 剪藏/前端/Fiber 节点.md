---
title: Fiber 节点
aliases:
  - Fiber 节点 —— 一个数据结构如何承载整个 React 运行时
tags:
  - type/clipping
  - topic/react
  - react/fiber
created: 2026-07-09
updated: 2026-08-14
source: "https://juejin.cn/post/7659763781161730102"
author:
  - [[老王以为]]
published: 2026-07-08
description: React通过 Fiber 节点去承载整个 React 的运行时，今天我们通过这篇文章来梳理一下 React 是如何做的，为什么要这样做，这样做背后的考量是什么？
---

> **内存泄漏追查记**
> 
> 之前接手了一个运行了两年的 React 项目。用户反馈说页面运行久了会变慢，刷新后才恢复。Chrome DevTools 的 Memory 面板显示，每次路由切换后都有大约 15MB 的内存无法被回收。15MB 听起来不多，但用户的办公电脑开着页面一整天，累积起来就是几个 GB。
> 
> 我打开了 Heap Snapshot，准备抓内存泄漏。结果让我愣在原地。
> 
> 泄漏的对象不是闭包，不是事件监听器，不是全局变量——而是 **FiberNode** 。成千上万个 FiberNode 对象躺在堆里，每个大约 300 字节，但它们像藤蔓一样互相纠缠，child 指向一个，sibling 指向另一个，return 指向上一个，alternate 指向另一棵树的对应节点。这些指针织成了一张巨大的网，垃圾回收器在这张网里迷路了，不知道哪些节点该回收，哪些不该。
> 
> 我花了三天时间追踪这些 FiberNode 的来龙去脉。第二天，我在 `ReactFiber.js` 里盯着 `FiberNode` 构造函数看了整整一个小时。那是一段朴素的代码——没有精巧的算法，没有复杂的设计模式，就是一堆 `this.xxx = null` 。但正是这堆赋值语句，定义了 React 运行时世界的全部。
> 
> ```javascript
> 代码解读复制代码function FiberNode(tag, pendingProps, key, mode) {
>   this.tag = tag;
>   this.key = key;
>   this.elementType = null;
>   this.type = null;
>   this.stateNode = null;
>   this.return = null;
>   this.child = null;
>   this.sibling = null;
>   this.index = 0;
>   this.ref = null;
>   this.pendingProps = pendingProps;
>   this.memoizedProps = null;
>   this.memoizedState = null;
>   this.updateQueue = null;
>   this.dependencies = null;
>   this.mode = mode;
>   this.flags = NoFlags;
>   this.subtreeFlags = NoFlags;
>   this.lanes = NoLanes;
>   this.childLanes = NoLanes;
>   this.alternate = null;
>   // ...
> }
> ```
> 
> 我数了数，二十多个字段。不多。但每一个字段都肩负着一项使命——有的描述组件的身份，有的链接树的结构，有的记录工作的状态，有的标记副作用。二十多个字段的组合，撑起了 React 的全部运行时——组件树、状态管理、调度优先级、副作用追踪、双缓冲……全都在这里。
> 
> 我关掉 DevTools，喝了口冷掉的咖啡。问题的根因找到了——一个第三方路由库在卸载时忘记了断开 Fiber 树的根节点引用。三行代码的修复。但那两天里我对 Fiber 数据结构的理解，比过去一年都深。

---

## 一、Virtual DOM 不够用了

早期的 React（v15 及之前）用 Virtual DOM 来描述界面。Virtual DOM 是什么？本质上是 ReactElement 的树——嵌套的对象结构，每个对象有 `type` 、 `props` 、 `children` 。

```javascript
代码解读复制代码{
  type: 'div',
  props: {
    className: 'container',
    children: [
      { type: 'h1', props: { children: 'Hello' } },
      { type: 'p', props: { children: 'World' } }
    ]
  }
}
```

这种结构有两个致命问题。

**第一，它只描述了"应该长什么样"，没有描述"要做什么工作"。** 每次 `setState` ，React 需要从头遍历整棵树，比较新旧差异，决定哪些节点要更新。遍历过程中产生的中间状态——走到哪了、下一个该去哪、有哪些副作用需要执行——全部存在 JavaScript 的调用栈上。结果就是遍历一旦开始就无法中断，因为调用栈里的信息在函数返回后就丢失了。

**第二，它是不可变的。** 每次更新都创建一棵全新的树。旧树丢弃，新树接管。这对于简单的应用没问题，但对于需要增量更新的场景（如动画、高频率输入），性能代价巨大。

Fiber 数据结构就是为了解决这两个问题而生的。它的核心设计判断是： **节点不应该只描述 UI，还应该描述工作。每个 Fiber 节点就是一个工作单元——它知道自己是什么类型的组件、链接到哪些上下游节点、有什么待处理的更新、属于什么优先级、需要什么副作用。**

这是数据结构层面的范式转换——从"描述 UI 的树"到"描述工作的链表"。

---

## 二、Fiber 节点的四重身份

打开 `packages/react-reconciler/src/ReactInternalTypes.js` ，看看 Fiber 类型的完整定义：

```javascript
代码解读复制代码// https://github.com/facebook/react/blob/main/packages/react-reconciler/src/ReactInternalTypes.js
export type Fiber = {
  // ===== 第一重身份：组件描述 =====
  tag: WorkTag,              // 组件类型标记（函数/类/Host/文本...）
  key: null | string,        // React key
  elementType: any,          // ReactElement.type（未解析）
  type: any,                 // 解析后的组件（函数引用/类/字符串标签）
  stateNode: any,            // 关联的真实实例（DOM 节点/组件实例）

  // ===== 第二重身份：树的结构 =====
  return: Fiber | null,      // 父节点
  child: Fiber | null,       // 第一个子节点
  sibling: Fiber | null,     // 下一个兄弟节点
  index: number,             // 在父节点 children 中的位置

  // ===== 第三重身份：工作状态 =====
  pendingProps: any,         // 新的 props（待处理）
  memoizedProps: any,        // 上一次 render 使用的 props
  memoizedState: any,        // 上一次 render 使用的 state
  updateQueue: mixed,        // 状态更新队列
  dependencies: Dependencies | null,  // Context 依赖
  mode: TypeOfMode,          // 运行模式
  lanes: Lanes,              // 本节点的工作优先级
  childLanes: Lanes,         // 子树的工作优先级

  // ===== 第四重身份：副作用追踪 =====
  flags: Flags,              // 本节点的副作用
  subtreeFlags: Flags,       // 子树的副作用
  deletions: Array<Fiber> | null,  // 需要删除的子节点
  alternate: Fiber | null,   // 双缓冲中的对应节点
  ref: any,                  // ref 引用
};
```

这四个维度的组合，让一个 Fiber 节点同时扮演了四种角色：

| **维度** | **对应字段** | **回答的问题** |
| --- | --- | --- |
| 组件描述 | tag, key, elementType, type, stateNode | "我是谁？" |
| 树的结构 | return, child, sibling, index | "我在哪？" |
| 工作状态 | pendingProps, memoizedProps, memoizedState, updateQueue, lanes | "我要做什么？" |
| 副作用追踪 | flags, subtreeFlags, deletions, alternate | "我做完了要通知谁？" |

![](https://p6-xtjj-sign.byteimg.com/tos-cn-i-73owjymdk6/76c0d5e0bd2847878d4f0738c14c2bc0~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ICB546L5Lul5Li6:q75.awebp?rk3s=f64ab15b&x-expires=1784082596&x-signature=zEOgZV5%2BQulPSSShat728AdPOdc%3D)

每一个字段都是运行时某个功能的必要载体。删掉任何一个字段，React 的某个功能就会损坏。 `alternate` 删掉，双缓冲没了。 `lanes` 删掉，并发调度没了。 `flags` 删掉，DOM 更新不知道要干什么。

---

## 三、从源码里读懂每个字段

### 3.1 ReactWorkTags.js —— 32 种 Fiber 的身份牌

先看看 `tag` 字段。它不是随便一个数字，而是来自 `ReactWorkTags.js` 的枚举：

```javascript
代码解读复制代码// https://github.com/facebook/react/blob/main/packages/react-reconciler/src/ReactWorkTags.js
export const FunctionComponent = 0;
export const ClassComponent = 1;
export const HostRoot = 3;           // 树根节点
export const HostPortal = 4;         // Portal 子树入口
export const HostComponent = 5;      // DOM 元素（div, span...）
export const HostText = 6;           // 文本节点
export const Fragment = 7;
export const Mode = 8;               // StrictMode / ConcurrentMode
export const ContextConsumer = 9;
export const ContextProvider = 10;
export const ForwardRef = 11;
export const Profiler = 12;
export const SuspenseComponent = 13;
export const MemoComponent = 14;
export const SimpleMemoComponent = 15;
export const LazyComponent = 16;
export const IncompleteClassComponent = 17;
export const DehydratedFragment = 18;
export const SuspenseListComponent = 19;
export const ScopeComponent = 21;
export const OffscreenComponent = 22;
export const LegacyHiddenComponent = 23;
export const CacheComponent = 24;
export const TracingMarkerComponent = 25;
export const HostHoistable = 26;     // 可提升资源（link preload）
export const HostSingleton = 27;     // 单例元素（html, head, body）
export const IncompleteFunctionComponent = 28;
export const Throw = 29;             // 用于抛出错误的特殊组件
export const ViewTransitionComponent = 30;
export const ActivityComponent = 31;
```

32 种类型。从最常见的 `FunctionComponent` （0）到专门用于错误处理的 `Throw` （29），从代表 DOM 元素的 `HostComponent` （5）到服务端渲染激活相关的 `DehydratedFragment` （18）。每种类型在 `beginWork` 和 `completeWork` 中有自己的处理逻辑。

`tag` 字段是 reconciler 的"路由表"。当 `beginWork` 处理一个 Fiber 节点时，第一个判断就是 `switch (fiber.tag)` ——不同 tag 走不同的分支。函数组件需要调用函数获取子节点，类组件需要调用 render 方法，Host 组件需要创建 DOM 元素，Suspense 组件需要检查是否已 resolve……路由决策完全基于这个小小的整数。

### 3.2 FiberNode 构造函数 —— 二十多个字段的诞生现场

看看 `ReactFiber.js` 中的 `FiberNode` 构造函数：

```javascript
代码解读复制代码// https://github.com/facebook/react/blob/main/packages/react-reconciler/src/ReactFiber.js
function FiberNode(tag, pendingProps, key, mode) {
  // --- Instance（实例标识） ---
  this.tag = tag;
  this.key = key;
  this.elementType = null;    // ReactElement.type（未解析的原始值）
  this.type = null;           // 解析后的值（函数引用/类构造函数/DOM 标签字符串）
  this.stateNode = null;      // 关联的真实平台实例

  // --- Fiber（链表指针） ---
  this.return = null;         // 父 Fiber
  this.child = null;          // 第一个子 Fiber
  this.sibling = null;        // 下一个兄弟 Fiber
  this.index = 0;             // 在父节点 children 中的位置索引
  this.ref = null;            // ref 引用
  this.refCleanup = null;     // ref 清理函数（useImperativeHandle 等）

  // --- Input / Output（输入输出） ---
  this.pendingProps = pendingProps;  // 新的 props
  this.memoizedProps = null;         // 上一次 render 使用的 props
  this.updateQueue = null;           // 更新队列（state、callbacks）
  this.memoizedState = null;         // 上一次 render 使用的 state
  this.dependencies = null;          // Context 依赖列表

  // --- Mode（运行模式） ---
  this.mode = mode;           // ConcurrentMode / StrictMode / NoMode

  // --- Effects（副作用标记） ---
  this.flags = NoFlags;               // 本节点需要执行的副作用
  this.subtreeFlags = NoFlags;        // 子树中需要执行的副作用
  this.deletions = null;              // 需要删除的子节点列表

  // --- Scheduling（调度） ---
  this.lanes = NoLanes;       // 本节点需要处理的工作的优先级
  this.childLanes = NoLanes;  // 子树需要处理的工作的优先级

  // --- Alternate（双缓冲） ---
  this.alternate = null;      // 指向另一棵树的对应节点

  // --- Profiler（性能分析，DEV 模式） ---
  if (enableProfilerTimer) {
    this.actualDuration = 0;
    this.actualStartTime = -1;
    this.selfBaseDuration = 0;
    this.treeBaseDuration = 0;
  }
}
```

这段代码朴素得让人惊讶。没有继承链，没有复杂的初始化逻辑，就是一个函数，一堆 `this.xxx = yyy` 。但正是这种朴素，赋予了 Fiber 节点极高的灵活性——它是一个纯数据对象，可以被任意复制、修改、丢弃、重建，而不需要担心隐藏的副作用。

注意 `elementType` 和 `type` 的区别——这两个字段经常让人困惑：

| **字段** | **值（函数组件）** | **值（类组件）** | **值（DOM 元素）** |
| --- | --- | --- | --- |
| `elementType` | 函数引用（ `App` ） | 类构造函数（ `MyComponent` ） | 字符串（ `'div'` ） |
| `type` | 同上 | 同上 | 同上 |

大多数情况下它们相等。但在 `ForwardRef` 和 `Lazy` 组件中， `elementType` 和 `type` 不同—— `elementType` 是外层包装（ `REACT_FORWARD_REF_TYPE` ）， `type` 是内部的真实组件。Reconciler 用 `elementType` 判断是否需要重新渲染（比较 key 和 type），用 `type` 执行实际的组件调用。

### 3.3 链表指针 —— child / sibling / return

这三个字段是 Fiber 架构的灵魂。它们把一个层次化的组件树，转换成了一个可以 **遍历、中断、恢复** 的链表结构。

![](https://p6-xtjj-sign.byteimg.com/tos-cn-i-73owjymdk6/d8a10ced249145e5a0e08b1775451770~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ICB546L5Lul5Li6:q75.awebp?rk3s=f64ab15b&x-expires=1784082596&x-signature=8xjgBmn%2FJR2LmEe8PYeOtJxxeAY%3D)

**child** ：指向第一个子节点。如果一个组件有多个子节点，只能通过 `child` 找到第一个，然后通过 `sibling` 链遍历其余的。

**sibling** ：指向下一个兄弟节点。 `child → sibling → sibling → null` 这条链，完整地表示了父节点的所有子节点。

**return** ：指向父节点。为什么叫 return 不叫 parent？因为这是从 **遍历视角** 命名的——处理完当前节点后，"返回到"哪个节点继续处理。在深度优先遍历中，处理完一个叶子节点后，要 return 到它的父节点，然后检查父节点的 sibling。return 比 parent 更精确地表达了这个语义。

这三个指针的组合，让 Fiber 树可以表达任意复杂的嵌套结构——但遍历它不需要递归，只需要一个 `while` 循环和一个当前指针。遍历过程中的"位置信息"完全保存在对象图里，不在调用栈上。这就是 Fiber 可以中断和恢复的根本原因。

### 3.4 lanes 与 childLanes —— 优先级传播的管道

`lanes` 和 `childLanes` 是 Fiber 并发调度的核心字段。它们都是 31 位的整数，用位掩码表示不同的更新优先级。

- **`lanes`** ：这个 Fiber 节点 **自身** 有待处理的工作。比如用户在这个组件上调用了 `setState` ，它的 `lanes` 字段会被标记上对应的优先级。
- **`childLanes`** ：这个 Fiber 节点的 **子树** 中有待处理的工作。如果子树深处的某个组件有更新，这个更新信息会通过 `childLanes` 向上传播到所有祖先节点。

为什么需要 `childLanes` ？因为 `workLoop` 需要快速判断"一棵子树里有没有工作要做"。如果没有 `childLanes` ，workLoop 需要遍历整棵子树才能确定是否有待处理的工作——这在大型应用中不可接受。有了 `childLanes` ，workLoop 只需要检查根节点的 `childLanes` ——如果为 0，整棵树都没有工作；如果非 0，沿着标记了优先级的分支向下查找。

![](https://p6-xtjj-sign.byteimg.com/tos-cn-i-73owjymdk6/9db28b3b2df94b2ca0b12a51423b5a20~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ICB546L5Lul5Li6:q75.awebp?rk3s=f64ab15b&x-expires=1784082596&x-signature=%2FHjlYW6tyNMF8FShWdn433XBwYM%3D)

`Counter` 调用了 `setState` ， `Counter.lanes` 被标记为 `DefaultLane` 。这个标记向上传播—— `Counter.return` （ `App` ）的 `childLanes` 被标记， `App.return` （ `HostRoot` ）的 `childLanes` 也被标记。当调度器检查是否有工作时，它看到 `HostRoot.childLanes !== 0` ，就知道子树里有工作要做。但它不需要检查 `Header` 分支——因为 `App.childLanes` 只标记了 `Counter` 所在的分支， `Header` 的 `childLanes` 为 0，可以被安全跳过。

### 3.5 flags 与 subtreeFlags —— 副作用的收集器

`flags` 和 `subtreeFlags` 是 Fiber 副作用系统的核心。它们也是位掩码，每一位代表一种副作用类型：

```javascript
代码解读复制代码// https://github.com/facebook/react/blob/main/packages/react-reconciler/src/ReactFiberFlags.js
export const NoFlags = /*                      */ 0b000000000000000000000000000;
export const Placement = /*                    */ 0b000000000000000000000000010;
export const Update = /*                       */ 0b000000000000000000000000100;
export const PlacementAndUpdate = /*           */ 0b000000000000000000000000110;
export const Deletion = /*                     */ 0b000000000000000000000001000;
export const ChildDeletion = /*                */ 0b000000000000000000000010000;
export const ContentReset = /*                 */ 0b000000000000000000000100000;
export const Callback = /*                     */ 0b000000000000000000001000000;
export const DidCapture = /*                   */ 0b000000000000000000010000000;
export const ForceClientRender = /*            */ 0b000000000000000000100000000;
export const Ref = /*                          */ 0b000000000000000001000000000;
export const Snapshot = /*                     */ 0b000000000000000010000000000;
export const Passive = /*                      */ 0b000000000000000100000000000;
export const Visibility = /*                   */ 0b000000000000001000000000000;
export const StoreConsistency = /*             */ 0b000000000000010000000000000;
export const HostEffectMask = /*               */ 0b000000000000011111111111111;
export const Hydrating = /*                    */ 0b000000000000100000000000000;
export const BeforeMutationMask = /*           */ 0b000000000000010000000100100;
export const MutationMask = /*                 */ 0b000000000000001010000010110;
export const LayoutMask = /*                   */ 0b000000000000000001000001000;
export const PassiveMask = /*                  */ 0b000000000000000100000000000;
```

| **Flag** | **含义** |
| --- | --- |
| `Placement` | 这是一个新节点，需要插入到 DOM 中 |
| `Update` | 节点的 props 或 state 发生了变化，需要更新 |
| `Deletion` | 节点需要被删除 |
| `ChildDeletion` | 子节点中有被删除的（用于清理 refs） |
| `Ref` | 节点的 ref 需要更新（attach 或 detach） |
| `Snapshot` | 类组件的 `getSnapshotBeforeUpdate` 需要调用 |
| `Passive` | `useEffect` 回调需要执行 |
| `Visibility` | `Offscreen` 组件的可见性发生了变化 |

**`flags`** 标记 **本节点** 的副作用。当 `beginWork` 处理一个 Fiber 节点时，如果发现它需要被插入（新创建的节点）或更新（props 变化了），就在 `flags` 字段上打上对应的位。

**`subtreeFlags`** 标记 **子树** 中所有副作用的按位或。 `completeWork` 在"完成"一个节点时（即它的所有子节点都已处理完毕），会把子节点的 `flags` 和 `subtreeFlags` 合并到自己的 `subtreeFlags` 中。这样，当遍历到达根节点时， `subtreeFlags` 包含了整棵树的所有副作用类型——commit 阶段只需要检查根节点的 `subtreeFlags` ，就能决定需要执行哪些阶段的副作用处理。

`deletions` 字段是一个特殊的数组。当一个子节点需要被删除时，它不是在自己的 `flags` 上标记 `Deletion` ——而是被放入父节点的 `deletions` 数组中。为什么？因为被删除的节点不会再参与后续的遍历，如果副作用标记在它自己身上，commit 阶段可能找不到它。放入父节点的 `deletions` 数组，确保了 commit 阶段能可靠地执行清理操作。

### 3.6 alternate —— 双缓冲的桥梁

`alternate` 是 Fiber 双缓冲机制的核心字段。每个 Fiber 节点都有一个 `alternate` ，指向另一棵树（current 或 workInProgress）中的对应节点。

![](https://p6-xtjj-sign.byteimg.com/tos-cn-i-73owjymdk6/2937e87887d24feab7e72b3e8bad880c~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ICB546L5Lul5Li6:q75.awebp?rk3s=f64ab15b&x-expires=1784082596&x-signature=Emd%2BwD7863Cgx4QKAqAXN5E9T74%3D)

当 React 开始一次新的渲染时，它会从 current 树的根节点出发，通过 `createWorkInProgress` 创建 workInProgress 树。这个过程中，每创建一个新的 workInProgress Fiber，就通过 `alternate` 字段和 current Fiber 双向链接：

```javascript
代码解读复制代码// https://github.com/facebook/react/blob/main/packages/react-reconciler/src/ReactFiber.js
workInProgress.alternate = current;
current.alternate = workInProgress;
```

`alternate` 链接带来的好处是巨大的：

1. **比较新旧状态** ：render 阶段可以随时通过 `workInProgress.alternate.memoizedProps` 获取上一次 render 的 props，决定是否需要重新渲染。
2. **复用节点** ： `createWorkInProgress` 优先复用 `alternate` 节点，减少对象创建和垃圾回收压力。
3. **快速切换** ：commit 阶段只需要交换根节点的指针， `current` 变成 `workInProgress` ， `workInProgress` 变成旧的 `current` 。原子操作，没有中间状态。

---

## 四、根节点：FiberRootNode 与 RootFiber 的区别

我一直有一个问题： `createRoot(container)` 创建的"根"到底是个什么东西？为什么有两个"根"的概念？

React 的"根"实际上是一个 **双层结构** ：

**FiberRootNode** （根容器）：由 `createFiberRoot` 创建。它不是 Fiber，是一个独立的对象类型。它管理整个 React 应用的 **全局状态** —— `containerInfo` （容器 DOM 节点）、 `pendingLanes` （所有待处理的优先级）、 `callbackNode` （调度回调）、 `onUncaughtError` / `onCaughtError` （错误处理回调）。一个 React 应用通常只有一个 FiberRootNode。

**RootFiber** （根 Fiber）：是一个 `tag = HostRoot` 的 Fiber 节点。它是 Fiber 树的 **入口点** ——所有组件都是它的后代。它有 `alternate` ，参与双缓冲。RootFiber 的 `stateNode` 指向 FiberRootNode，FiberRootNode 的 `current` 指向 current 树的 RootFiber。

![](https://p6-xtjj-sign.byteimg.com/tos-cn-i-73owjymdk6/c0d90f7c696b474c9d19e1c2ecbf4563~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ICB546L5Lul5Li6:q75.awebp?rk3s=f64ab15b&x-expires=1784082596&x-signature=jLv74e3PauhieSNj%2BoUCa4TpAVA%3D)

为什么要两层？因为 FiberRootNode 不参与 Fiber 树的遍历和双缓冲。它的字段（如 `pendingLanes` 、 `callbackNode` ）是全局性的，不需要"两个版本"。如果把所有字段都放在 RootFiber 上，双缓冲时就需要复制这些全局状态，既浪费内存又容易出错。分层设计让"全局状态"和"树节点"各自归位。

```javascript
代码解读复制代码// https://github.com/facebook/react/blob/main/packages/react-reconciler/src/ReactInternalTypes.js
export type FiberRoot = {
  // 容器信息
  tag: RootTag,                          // LegacyRoot / ConcurrentRoot
  containerInfo: Container,              // DOM 容器节点
  // 树指针
  current: Fiber,                        // 指向 current 树的 RootFiber
  // 调度状态
  callbackNode: mixed,                   // Scheduler 的回调句柄
  callbackPriority: Lane,                // 当前回调的优先级
  pendingLanes: Lanes,                   // 所有待处理的 lane
  expiredLanes: Lanes,                   // 已过期的 lane（必须同步执行）
  // 事件处理
  onUncaughtError: (error: mixed, errorInfo: {...}) => void,
  onCaughtError: (error: mixed, errorInfo: {...}) => void,
  onRecoverableError: (error: mixed, errorInfo: {...}) => void,
  // ... 更多字段
};
```

---

## 五、为一个复杂运行时设计核心数据结构

位掩码是性能优化的利器

Fiber 的 `lanes` 、 `flags` 、 `subtreeFlags` 都是位掩码。位运算在 JavaScript 中虽然是 32 位限制，但对于表达"多选状态"（一个节点可以有多个副作用、多个优先级）来说，位掩码比数组或对象高效得多：

- **按位或** （ `|` ）添加标记： `flags |= Placement`
- **按位与** （ `&` ）检查标记： `if (flags & Placement) { ... }`
- **按位非** （ `~` ）和与（ `&` ）清除标记： `flags &= ~Placement`
- **按位与** （ `&` ）提取最低位的 1： `lanes & -lanes` 获取最高优先级

这些操作都是 O(1) 的，在现代 JavaScript 引擎中可以被高度优化。

### 好的数据结构能让团队并行工作

Fiber 数据结构的四重身份分离——组件描述、树结构、工作状态、副作用追踪——让不同的团队成员可以专注于不同的领域：

- 负责组件模型的工程师关注 `tag` 、 `type` 、 `memoizedState`
- 负责调度系统的工程师关注 `lanes` 、 `childLanes`
- 负责渲染的工程师关注 `flags` 、 `subtreeFlags` 、 `stateNode`
- 负责 DevTools 的工程师关注 `alternate` 、 `ref`

这些字段共同定义在 Fiber 类型中，构成了团队的 **共享契约** 。每个人都在同一个数据结构上工作，但关注点不同。

| **React Fiber 的设计** | **迁移策略** |
| --- | --- |
| 四重身份分离（组件/树/工作/副作用） | 核心数据实体按职责维度拆分字段组，每个团队负责一组 |
| 位掩码表达多选状态 | 状态和标记用位运算管理，减少数组遍历和对象嵌套 |
| childLanes 向上传播 | 树形结构中需要"快速判断子树是否有工作"时，用聚合字段避免全量遍历 |
| alternate 双缓冲 | 任何需要"准备新版本再原子切换"的场景，维护两个版本的并行结构 |
| FiberRootNode 与 RootFiber 分层 | 全局状态与树节点状态分离，避免双缓冲时复制全局数据 |

---

## 六、二十多个字段，撑起一个世界

回到那个 FiberNode 构造函数。

```javascript
代码解读复制代码function FiberNode(tag, pendingProps, key, mode) {
  this.tag = tag;
  this.key = key;
  this.elementType = null;
  this.type = null;
  this.stateNode = null;
  this.return = null;
  this.child = null;
  this.sibling = null;
  this.index = 0;
  this.ref = null;
  this.pendingProps = pendingProps;
  this.memoizedProps = null;
  this.memoizedState = null;
  this.updateQueue = null;
  this.dependencies = null;
  this.mode = mode;
  this.flags = NoFlags;
  this.subtreeFlags = NoFlags;
  this.lanes = NoLanes;
  this.childLanes = NoLanes;
  this.alternate = null;
}
```

二十多个字段。没有魔法，没有黑科技。但它们的组合方式，支撑了现代前端框架中最复杂的运行时系统之一。

`tag` 决定了"我是谁"。 `child` / `sibling` / `return` 决定了"我在哪里，和谁连接"。 `pendingProps` / `memoizedState` / `updateQueue` 决定了"我要做什么"。 `lanes` / `childLanes` 决定了"什么时候做"。 `flags` / `subtreeFlags` / `deletions` 决定了"做完之后要通知谁"。 `alternate` 决定了"如果被打断了，怎么恢复到刚才的状态"。

这些字段的组合，让 React 能够：在 16 毫秒的帧间隙里穿插多个不同优先级的更新；在渲染过程中响应用户输入而不丢失位置信息；在 commit 之前安全地丢弃不完整的工作；在 DOM 操作完成后再调用生命周期方法。

一个好的数据结构，就像一个好的建筑结构——它不显眼，但它决定了整个系统的可能性和上限。Fiber 数据结构是 React 的骨架。Hooks、Concurrent Mode、Suspense、Server Components——这些华丽的上层建筑，都站在这二十多个字段的肩膀之上。

## 相关笔记

- [[React 组件状态与闭包]]
