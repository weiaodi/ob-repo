---
type: summary
theme: 编程与技术
source: 豆包/raw/编程与技术/浏览器、CSS与前端工程.md
created: 2026-10-16
---

# 浏览器、CSS与前端工程

> 这组对话很杂但主线清晰：CSS 布局与定位的「为什么不符合直觉」类追问（父子距离、margin 塌陷、滚动）、浏览器渲染机制（回流/重绘、RenderTree、rAF、跨域/跨站、缓存协商），以及前端工程化（webpack vs vite、Tree Shaking、Code Splitting、Web Worker、Web Components）与大型列表、白屏监控等实战问题。

## 核心要点
- 移动子元素后父子距离不等于位移量，常见原因是 margin 塌陷（垂直方向父子共用边距）、其他定位属性、元素尺寸变化和浏览器渲染差异。
- DOM Tree 含全部节点（含 display:none 元素），RenderTree 只含需要显示的节点；`display:none` 不在 RenderTree，`visibility:hidden` 仍在并占位。
- 回流（layout）由尺寸/位置/增删可见元素/字体与窗口尺寸变化触发；重绘只在颜色、背景、阴影等外观改变时触发，回流必然伴随重绘。
- `requestAnimationFrame` 的回调在浏览器每次重绘之前执行（约 60Hz 同步），比 setTimeout/setInterval 更平滑、不易过度绘制。
- 构建工具差异：webpack 开发时先整体打包再起服务（启动慢、可配置性强）；Vite 开发态直接用浏览器原生 ESM 按需加载，生产用 Rollup 打包，启动与 HMR 更快。
- Tree Shaking 与 Code Splitting 都发生在构建阶段；Code Splitting 主要方式有动态 `import()`、路由懒加载、多入口。
- 长列表（约 1 万项）不要逐项绑事件，用事件委托把监听挂在父元素上靠冒泡处理；更极致可用虚拟滚动只渲染可视区域。
- 缓存：强缓存由 Cache-Control / Expires 控制不发请求；协商缓存用 Last-Modified + If-Modified-Since 或 ETag + If-None-Match 校验，未变更返回 304。
- 跨域与跨站不是一回事：跨域看协议+域名+端口三要素（管 AJAX/fetch，靠 CORS/JSONP/代理），跨站只看根域名（管 Cookie 的 SameSite，防 CSRF）；所有跨站一定跨域，反之不成立。
- 大规模游离 DOM（脱离文档流但仍被引用）难回收，排查点包括未移除的事件监听、全局变量缓存、闭包持有引用；核心是切断引用链（remove + 置 null + 清空数组）。

## 涉及概念
[[事件委托]]、[[回流与重绘]]、[[协商缓存]]、[[同源策略]]、[[Web Worker]]、[[依赖注入]]、[[虚拟DOM]]

## 值得回看的问答
- **移动120px后，再计算父子距离为什么不是120px**：典型原因是垂直方向 margin 塌陷——父子边距合并导致整体下移而非子元素相对父元素下移。
- **浏览器中的回流和重绘，在vue中哪些操作会触发**：改尺寸位置类样式、v-if/v-show 切换、v-html/v-text 内容变化、响应式数据引起布局变化、window resize 都会触发回流（外观类样式只触发重绘）。
- **webpack和vite的区别**：webpack 一切皆模块、先打包后开发、配置灵活；Vite 开发态原生 ESM 按需加载、生产用 Rollup，启动和热更新更快。
- **.Last-Modify和Etag怎么实现的**：服务器分别返回最后修改时间/资源唯一标识，浏览器下次带 If-Modified-Since 或 If-None-Match，未变更则返回 304 用本地缓存。
- **跨域和跨站有什么区别？**：跨域=协议+域名+端口三元对比、管接口请求；跨站=只比根域名、管 Cookie 与 CSRF 安全；子域名之间属于「跨域但同站」。
- **一个系统的快捷键模块，依赖注入方式的写法和直接写一个快捷键组合和回调函数的方式哪个合理**：快捷键少、稳定且已有冲突检测时直接硬编码性价比更高；依赖注入的价值在批量调整、跨模块复用、统一禁用这类规模化需求。

## 相关原始资料
[[豆包/raw/编程与技术/浏览器、CSS与前端工程]]
