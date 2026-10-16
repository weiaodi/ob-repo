---
type: summary
theme: 编程与技术
source: 豆包/raw/编程与技术/Vue与React框架.md
created: 2026-10-16
---

# Vue与React框架

> 这组对话围绕 Vue 与 React 的框架机制对比：响应式/虚拟 DOM 两种范式、资源调度与更新时机、Diff 算法实现差异，以及 Svelte 与 Angular 作为第三条路线。也包含 Vue 的 ref/provide-inject、React Hooks 与生命周期的对应关系、setState 异步等常见面试点。

## 核心要点
- Vue 的 `ref` 在组件挂载（mounted）后才拿到真实实例，父组件通过 `$refs` 访问子组件；跨层级共享用 `provide`/`inject`（依赖注入）而非逐层传 props。
- React 并非「没有生命周期」：类组件保留 componentDidMount 等钩子，Hooks 用 `useEffect`、`useLayoutEffect` 对应挂载/更新/卸载，`useLayoutEffect` 在 DOM 更新后、浏览器绘制前同步执行。
- 调度机制对比：Vue 靠响应式依赖收集 + 异步更新队列（同一事件循环内变更被缓冲，在微任务阶段统一刷新）；React 靠虚拟 DOM + Diff + 批量更新，Fiber 架构可按优先级拆分、暂停、恢复任务。
- Diff 差异：Vue 用双端比较（两端指针向中间收敛）+ 编译期 patchFlag 标记；React 递归深度优先比较、靠 key 判断节点可否复用，Fiber 把更新拆成小任务做优先级调度。
- 更新粒度：Vue 组件级依赖追踪，精确更新受影响的 DOM；React 默认整棵子树重新渲染，需 shouldComponentUpdate / React.memo 手动优化。
- `setState` 多数情况表现为异步：合成事件与生命周期中开启批量更新，多个 setState 被合并；在 `setTimeout`/`Promise` 等原生异步回调中无法批量，表现为同步。
- Svelte 走编译时路线：编译阶段分析依赖并直接生成原生 DOM 操作代码，运行时无虚拟 DOM 比较；Angular 默认脏检查，可用 OnPush 策略减少检测。

## 涉及概念
[[虚拟DOM]]、[[Diff算法]]、[[Fiber架构]]、[[响应式系统]]、[[setState]]、[[事件循环]]、[[依赖注入]]

## 值得回看的问答
- **react是单向数据流吗？**：是。数据通过 props 从父流向子，子组件不能直接改父数据，所以数据流向可预测。
- **react中setstate是异步吗？为什么**：多数场景异步，目的是批量合并更新、减少渲染次数、保证状态与 UI 一致；在原生异步回调中不受 React 控制，表现为同步。
- **vue和react中的diff算法有什么区别**：Vue 双端比较 + patchFlag 精确更新、组件级粒度；React 递归比较 + key 复用 + Fiber 优先级调度、默认整棵子树重渲染。
- **vue和react的资源调度策略有什么区别**：Vue 是响应式依赖收集 + 异步更新队列（微任务批量刷新）；React 是虚拟 DOM + Diff + 批量更新，并用 Fiber 依据浏览器空余帧与优先级调度。
- **react为什么没有生命周期概念**：这个说法不准确——类组件有完整生命周期方法，Hooks 只是换成了 useEffect 等表达方式。
- **vue ref实例化时间**：在 mounted 之后，此时 DOM 已创建并插入文档，父组件引用子组件 ref 同理。

## 相关原始资料
[[豆包/raw/编程与技术/Vue与React框架]]
