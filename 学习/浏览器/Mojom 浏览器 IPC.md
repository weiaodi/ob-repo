---
title: Mojom 浏览器 IPC
aliases:
  - mojom-浏览器ipc通讯模块
  - Mojo
tags:
  - type/note
  - topic/browser
  - browser/ipc
uid: 20250825170449
created: 2025-08-25-17-04-49
updated: 2026-08-14
---

# mojom-浏览器进程间通讯框架

> 文档链接：[https://chromium.googlesource.com/chromium/src/+/refs/heads/main/docs/mojo_and_services.md](https://chromium.googlesource.com/chromium/src/+/refs/heads/main/docs/mojo_and_services.md)

  

> 作用： 主要用于进程间通信，减少模块间依赖，同时用接口描述语言书写mojom接口文件，经过编译之后会自动生成对应的mojo类，

  

## Mojo 工作原理（How Mojo works）

Mojo 的工作方式与 Protobuf 十分相似，核心流程分为“接口定义”和“代码生成”两步：

1. 接口定义：开发者在 `mojom` 文件中定义各类接口，每个接口对应一个可被远程端点调用的函数。
    
2. 代码生成：编译阶段，`mojom` 文件会被编译为不同语言的“绑定代码（bindings）”。例如，一个 `example.mojom` 文件会生成 `example.mojom.h` 头文件，其他源文件可直接引入该头文件使用接口。
    

  

Mojo 为不同语言的绑定代码预设了一套源模板（source templates）。编译时，程序会先解析 `mojom` 文件生成抽象语法树（AST），再用 AST 中的数据渲染模板，最终生成可直接使用的代码。

  

例如在日志信息中定义的信息源：ConsoleMessageSource:

[https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/public/mojom/devtools/console_message.mojom;bpv=0;bpt=0](https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/public/mojom/devtools/console_message.mojom;bpv=0;bpt=0)

生成的模板内容：

```
/**
 * @const { {$: !mojo.internal.MojomType} }
 */
export const ConsoleMessageSourceSpec = { $: mojo.internal.Enum() };

/**
 * @enum {number}
 */
export const ConsoleMessageSource = {
  
  kXml: 0,
  kJavaScript: 1,
  kNetwork: 2,
  kConsoleApi: 3,
  kStorage: 4,
  kRendering: 5,
  kSecurity: 6,
  kOther: 7,
  kDeprecation: 8,
  kWorker: 9,
  kViolation: 10,
  kIntervention: 11,
  kRecommendation: 12,
  MIN_VALUE: 0,
  MAX_VALUE: 12,
};
```

还有其他平台对应的模板代码：

![](https://docs.corp.kuaishou.com/image/api/external/load/out?code=fcABIUxBA1lo1uhxM6OTMYT5v:7121503269019285320fcABIUxBA1lo1uhxM6OTMYT5v:1756196608464)

## Mojo网络

Mojo 网络由多个“节点（Node）”组成，每个节点对应一个运行 Mojo 的进程。Mojo 网络的工作模式与 TCP/IP 网络类似。

![](https://docs.corp.kuaishou.com/image/api/external/load/out?code=fcABIUxBA1lo1uhxM6OTMYT5v:-3574013342492053254fcABIUxBA1lo1uhxM6OTMYT5v:1756196608464)

上图展示了在两个进程间使用 Mojo 的数据流。它有以下几个特点：

1. Channel: Mojo 内部的实现细节，对外不可见，用于包装系统底层的通信通道 ；

2. Node: 每个进程只有一个 Node，它在 Mojo 中的作用相当于 TCP/IP 中的 IP 地址，同样是内部实现细节，对外不可见；

3. Port: 每个进程可以有上百万个 Port，它在 Mojo 中的作用相当于 TCP/IP 中的端口，同样是内部实现细节，对外不可见，每个 Port 都必定会对应一种应用层接口，目前 Mojo 支持三种应用层接口；

4. MessagePipe: 应用层接口，用于进程间的双向通信，类似 UDP,消息是基于数据报的，底层使用 Channel 通道；

5. DataPipe: 应用层接口，用于进程间单向块数据传递，类似 TCP,消息是基于数据流的，底层使用系统的 Shared Memory 实现；

6. SharedBuffer: 应用层接口，支持双向块数据传递，底层使用系统 Shared Memory 实现；

7. MojoHandle： 所有的 MessagePipe,DataPipe,SharedBuffer 都使用 MojoHandle 来包装，有了这个 Handle 就可以对它们进行读写操作。还可以通过 MessagePipe 将 MojoHandle 发送到网络中的任意进程。

8. PlatformHandle: 用来包装系统的句柄或文件描述符，可以将它转换为 MojoHandle 然后发送到网络中的任意进程。

## 相关笔记

- [[从打孔卡片到 Console]]
