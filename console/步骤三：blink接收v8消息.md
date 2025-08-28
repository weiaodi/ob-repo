---
uid: 20250825211243
tags: 
created: 2025-08-25-21-12-43
updated: 2025-08-25-21-12-43
---
步骤三：blink接收v8消息

[https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/renderer/core/exported/web_dev_tools_agent_impl.cc;bpv=0;bpt=1](https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/renderer/core/exported/web_dev_tools_agent_impl.cc;bpv=0;bpt=1)

```
// WebDevToolsAgentImpl::AttachSession 中的关键代码
session->CreateAndAppend<InspectorLogAgent>(
    // 依赖1：Blink 层面的控制台消息存储（存储日志）
    &inspected_frames->Root()->GetPage()->GetConsoleMessageStorage(),
    // 依赖2：性能监控器（用于日志的时间戳关联）
    inspected_frames->Root()->GetPerformanceMonitor(),
    // 依赖3：V8 调试会话（用于对接 V8 的 JS 日志和执行功能）
    session->V8Session()
);
```

### V8 日志的产生与转发流程

当用户在页面中执行 console.log('hello') 或 JS 代码抛出错误时，日志会通过 V8 → Blink → DevTools 的流程展示在 Console 面板，核心链路如下：

#### 步骤 1：V8 捕获 JS 日志并通知 Blink

- V8 引擎在执行 console.log 时，会触发其内置的「日志钩子」，并通过 v8::inspector::V8Inspector 接口，将日志事件（包含日志类型、内容、调用栈）发送给已绑定的 V8Session（即 session->V8Session()）。
    
- V8 错误（如语法错误、运行时错误）会通过 V8 的「异常捕获机制」，同样封装为日志事件发送给 V8Session。
    

#### 步骤 2：InspectorLogAgent 接收并处理 V8 日志

- InspectorLogAgent 内部会监听 V8Session 的日志事件（通过 V8 Inspector 提供的回调接口），并将 V8 日志转换为 Blink 统一的「控制台消息格式」（ConsoleMessage）。
    
- 转换过程中，会补充日志的元数据：
    

- 时间戳（从 PerformanceMonitor 获取）；
    
- 日志类型（Info/Warn/Error/Debug，对应 V8 日志的级别）；
    
- 关联的 DOM 节点（如日志由某个脚本标签执行产生，则关联该节点）；
    
- 调用栈信息（从 V8 提供的 v8::StackTrace 转换为 Blink 的 StackTrace）。
    

#### 步骤 3：日志同步到 DevTools Console 面板

- InspectorLogAgent 将处理后的日志，通过 DevTools 协议（CDP）的 Log.entryAdded 事件，转发给 DevTools 前端。
    
- DevTools 前端接收到事件后，根据日志类型渲染不同样式（如 Error 红色、Warn 黄色），并展示调用栈、跳转链接等交互元素。