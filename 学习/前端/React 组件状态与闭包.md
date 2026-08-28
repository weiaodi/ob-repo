---
title: React 组件状态与闭包
aliases:
  - React 组件实例的状态存储模型、函数组件重执行的变量创建机制、闭包与执行上下文的绑定关系
tags:
  - type/note
  - topic/react
  - react/hooks
  - react/fiber
uid: 20260211115450
created: 2026-02-11-11-54-50
updated: 2026-08-14
---

### 一、前置核心概念（先统一认知）

在讲解实现前，先明确 React 底层的几个关键抽象（这是理解的基础）：

|   |   |
|---|---|
|抽象概念|核心作用|
|Fiber 节点|React 组件实例的底层载体，每个组件实例对应一个 Fiber 节点，存储组件的「状态、DOM 信息、更新队列」等核心数据|
|Hook 链表|每个 Fiber 节点内部维护一个 Hook 链表（useState/useEffect 等 Hook 都对应链表中的一个 Hook 对象）|
|执行上下文（Execution Context）|函数组件每次执行时的独立环境，包含本次执行的所有变量、函数声明等，由 JS 引擎管理，执行完毕后若无闭包引用则被销毁|

### 二、React 中“状态保留”的实现逻辑（外部仓库的本质）

`useState` 的“状态保留”并非魔法，核心是将状态存储在 Fiber 节点的 Hook 链表中（组件实例的外部），而非函数组件的执行上下文里。

#### 1. 状态存储的物理位置

每个函数组件实例对应的 Fiber 节点中，有一个 `memoizedState` 属性，它指向该组件的 Hook 链表头节点。以 `const [count, setCount] = useState(0)` 为例：

- 第一次执行 `useState(0)` 时，React 会创建一个 `Hook` 对象：
    

```
  // 简化版 Hook 对象结构（React 源码中为 internal Hook 结构）
  const hook = {
    memoizedState: 0, // 存储状态的当前值（初始为 0）
    queue: null, // 存储更新队列（setCount 触发的更新会存入这里）
    next: null // 指向下一个 Hook（若有多个 useState）
  };
```

- 这个 `hook` 对象会被挂载到 Fiber 节点的 `memoizedState` 链表中，成为链表的第一个节点；
    
- 后续所有对 `count` 的更新（`setCount`），本质是修改这个 `hook.memoizedState` 的值——这就是“外部仓库”的物理载体。
    

#### 2. 状态保留的执行流程（组件重执行时如何拿到最新值）

函数组件每次重执行时，`useState` 的核心执行逻辑如下（简化版）：

```
function useState(initialState) {
  // 1. 获取当前组件的 Fiber 节点（React 内部通过全局变量跟踪）
  const fiber = getCurrentFiber();
  // 2. 获取当前执行到的 Hook 节点（通过“遍历指针”跟踪，每次 useState 执行指针后移）
  const hook = getCurrentHook();

  if (isMount) { // 组件首次挂载
    // 初始化 Hook 对象，存入初始值
    hook.memoizedState = initialState;
    fiber.memoizedState = hook; // 挂载到 Fiber 节点
  } else { // 组件重执行（更新）
    // 不创建新 Hook，直接复用 Fiber 中已有的 Hook 对象
  }

  // 3. 定义 setCount 函数：修改 Hook 对象的 memoizedState
  const dispatch = (action) => {
    // 将更新动作加入 Hook 的更新队列
    enqueueUpdate(fiber, hook, action);
    // 触发组件重执行
    scheduleUpdateOnFiber(fiber);
  };

  // 4. 返回 [当前状态值, 更新函数]
  return [hook.memoizedState, dispatch];
}
```

#### 核心结论：

- “状态保留”的本质是：状态值存储在 Fiber 节点的 Hook 对象中（组件实例的外部），而非函数组件的执行上下文里；
    
- 组件重执行时，`useState` 不会重新初始化状态，而是直接读取 Fiber 中 Hook 对象的 `memoizedState`（最新值）。
    

### 三、React 中“变量重建”的实现逻辑（执行上下文的独立性）

函数组件每次重执行，本质是JS 引擎重新执行该函数，创建全新的执行上下文，上下文内的所有变量都是全新的内存对象——这是 JS 函数执行的原生特性，React 并未干预，只是利用了这一特性。

#### 1. 变量重建的底层原因

JS 函数的执行遵循“执行上下文栈”规则：

- 每次调用函数（组件重执行本质是 React 调用该函数），JS 引擎会创建一个新的「执行上下文」；
    
- 执行上下文中的变量（如 `count`）是在函数执行时动态创建的，存储在该上下文的「变量环境（Variable Environment）」中；
    
- 不同执行上下文的变量环境是完全隔离的，变量的内存地址必然不同。
    

以 Counter 组件为例：

- 第一次执行：创建执行上下文 `EC1`，其中 `count` 变量的内存地址为 `0x100`，值为 `hook.memoizedState = 0`；
    
- 点击按钮触发更新：React 调用 Counter 函数，创建执行上下文 `EC2`，其中 `count` 变量的内存地址为 `0x200`，值为 `hook.memoizedState = 1`；
    
- `EC1` 和 `EC2` 是两个独立的执行上下文，`0x100` 和 `0x200` 是两个不同的内存地址——这就是“变量重建”的本质。
    

#### 2. React 对变量重建的“无干预”特性

React 作为框架，无法改变 JS 函数执行的原生规则：

- 它不能让两次执行的 `count` 变量复用同一个内存地址；
    
- 它只能保证“新执行上下文的变量能拿到 Fiber 中 Hook 的最新值”，但无法让“旧执行上下文的变量同步更新”。
    

### 四、过期闭包的根源：闭包与旧执行上下文的绑定

结合上述两点，过期闭包的实现层面根源可总结为：

1. 闭包的本质是「函数对象 + 该函数创建时的执行上下文」，JS 引擎会保留闭包所引用的执行上下文，使其不被垃圾回收；
    
2. 定时器回调（闭包）是在 `EC1`（第一次执行的上下文）中创建的，它引用了 `EC1` 中的 `count` 变量（地址 `0x100`）；
    
3. 组件重执行创建的 `EC2`、`EC3` 等上下文，与 `EC1` 完全隔离，`EC1` 中的 `count` 变量值永远是初始的 `0`（因为它只在 `EC1` 初始化时赋值过一次）；
    
4. React 只能更新 Fiber 中 Hook 的 `memoizedState`，并将其赋值给新上下文的变量，但无法修改旧上下文的变量值——因此闭包永远读不到新值。
    

#### 源码层面的关键佐证

React 源码中，`useEffect` 的回调函数会被封装为一个「Effect 对象」，挂载到 Fiber 节点的 `updateQueue` 中。该 Effect 对象会捕获创建时的「执行上下文」和「变量引用」：

```
// 简化版 useEffect 实现
function useEffect(create, deps) {
  const fiber = getCurrentFiber();
  const hook = getCurrentHook();

  if (isMount) {
    // 首次挂载：创建 Effect 对象，捕获 create 回调（闭包）
    const effect = {
      create, // 定时器回调所在的函数，绑定了旧执行上下文
      deps,
      next: null
    };
    fiber.updateQueue = effect;
  } else {
    // 更新时：对比依赖，决定是否重新执行 create
  }
}
```

由于 Effect 对象捕获的是「首次创建的 create 回调」，而该回调绑定了 `EC1`，因此哪怕后续组件重执行，回调中的变量引用也永远指向 `EC1` 的变量。

### 五、总结（实现层面的核心结论）

1. 状态保留的实现：React 将状态存储在组件实例对应的 Fiber 节点的 Hook 链表中（外部存储），组件重执行时，`useState` 读取 Hook 对象的最新值并赋值给新变量——这是“状态保留”的物理基础；
    
2. 变量重建的实现：函数组件重执行是 JS 引擎创建新执行上下文的过程，新上下文的变量是全新的内存对象，地址与旧变量完全不同——这是 JS 原生特性，React 无法干预；
    
3. 过期闭包的实现根源：闭包捕获的是旧执行上下文的变量引用，React 只能更新新上下文的变量值，无法修改旧上下文的变量值，因此闭包永远读不到新值。
    

这一逻辑是 React 函数组件模型的核心特性，也是其设计取舍的结果：React 选择利用 JS 函数的原生执行规则实现组件渲染，代价是引入了过期闭包这类问题；而 Solid.js 等框架则通过“将状态存储在全局响应式容器中，组件只执行一次”的设计，从根源规避了这一问题。

## 相关笔记

- [[Solid 与 React 状态管理]]
- [[Fiber 节点 —— 一个数据结构如何承载整个 React 运行时]]
