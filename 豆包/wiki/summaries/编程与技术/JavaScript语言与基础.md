---
type: summary
theme: 编程与技术
source: 豆包/raw/编程与技术/JavaScript语言与基础.md
created: 2026-10-16
---
# JavaScript语言与基础

> 这组对话集中在 JavaScript 语言本身：手写 Promise.all / JSONP / 防抖节流 / 深度克隆等高频实现题，配合 this 指向、事件循环微宏任务、栈堆内存、原型与属性遍历、基本类型包装对象等底层原理。整体偏「面试题 + 原理追问」的风格，问题往往会被连追 2~4 层。

## 核心要点
- 防抖 = 频繁触发只认最后一次，停止后延迟执行（电梯关门）；节流 = 固定时间窗口内只执行一次（技能 CD），前者用 clearTimeout 重置定时器，后者用标志位 + setTimeout 冷却。
- 事件循环：同步代码先跑完，再清空微任务队列，最后执行宏任务；`setTimeout` 是宏任务，`Promise.then/finally` 是微任务，因此 `1 3 7 4 5 6 2` 的经典输出顺序成立。
- 手写 Promise.all 的要点：`Promise.resolve()` 包装每个元素（兼容非 Promise 值）、用 completedCount 计数、结果按下标写回保证顺序、任一 reject 立即 reject；空数组直接 resolve。
- JSONP 依赖动态创建 `script` 标签 + 全局回调函数，URL 上带 `callback` 参数；只能发 GET、无法做严格错误处理，现代跨域已推荐用 CORS。
- 深度克隆需要同时处理循环引用（WeakMap）、Date / RegExp / Map / Set 等特殊类型，只复制原始值和非对象值直接返回。
- `typeof null === "object"` 是历史遗留：早期用值的低位（低 16 位）标记数据类型，null 在内存中全为 0 被判为对象，为了向后兼容一直保留。
- 基本类型（如字符串）能调用 String 方法，靠的是「临时包装对象」机制：创建对象 → 调用方法 → 销毁，且不改变原值。
- `for in` 遍历可枚举的键名（含原型链继承属性，需 hasOwnProperty 过滤），`for of` 要求迭代器协议（`Symbol.iterator`），普通对象没有迭代器所以不能直接用。

## 涉及概念
[[闭包]]、[[Promise.all]]、[[事件循环]]、[[防抖]]、[[节流]]、[[JSONP]]、[[深拷贝]]

## 值得回看的问答
- **实现一个 jsonp**：动态创建 script 标签，URL 带 `callback=handleResponse`，服务器返回 `handleResponse({...})` 即自动执行回调拿到 data；只能 GET，安全性不如 CORS。
- **JS实现promiseall方法**：new Promise 中遍历数组，`Promise.resolve` 包装每个元素，逐个 then 后计数，全部完成后 resolve 按序结果数组，任一失败即 reject。
- **console.log(1); setTimeout(() => console.log(2), 0); 后面还有 Promise 与 finally，输出顺序是什么**：`1 3 7 4 5 6 2`，即同步代码 → 微任务（then/finally）→ 宏任务（setTimeout）。
- **js中null为什么是对象呢？如何理解**：设计遗留问题，早期用低 16 位表示类型、000 表示对象，null 全 0 被误判，后来因向后兼容未修改。
- **写一个防抖和节流函数**：防抖每次触发清掉旧定时器重新计时；节流用 inThrottle 标志位锁住下限时间窗。
- **js手写深度克隆**：递归 + WeakMap 处理循环引用，并单独分支处理 Date、RegExp、Map、Set 等内置类型。

## 相关原始资料
[[豆包/raw/编程与技术/JavaScript语言与基础]]
