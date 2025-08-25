---
uid: 20250825170415
tags: 
created: 2025-08-25-17-04-15
updated: 2025-08-25-17-04-15
---


##### [https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/renderer/core/inspector/console_message.cc](https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/renderer/core/inspector/console_message.cc)

```
 
// Blink引擎中控制台消息的核心封装类，负责统一管理各类控制台消息的元信息
 
namespace blink {

// 构造函数1：处理网络请求相关的控制台消息
// 参数说明：
// - source：消息来源（如网络模块）
// - level：消息级别（错误/警告/日志等）
// - message：消息文本内容
// - url：相关资源URL
// - loader：文档加载器（用于关联请求）
// - request_identifier：网络请求唯一标识
ConsoleMessage::ConsoleMessage(mojom::blink::ConsoleMessageSource source,
                               mojom::blink::ConsoleMessageLevel level,
                               const String& message,
                               const String& url,
                               DocumentLoader* loader,
                               uint64_t request_identifier)
    : ConsoleMessage(source, level, message, CaptureSourceLocation(url, 0, 0)) {
  // 生成并存储网络请求唯一标识（用于关联网络面板中的请求）
  request_identifier_ =
      IdentifiersFactory::RequestId(loader, request_identifier);
}

// 构造函数2：处理Worker线程产生的控制台消息
// 参数说明：
// - level：消息级别
// - message：消息文本
// - location：消息来源代码位置（URL/行号等）
// - worker_thread：产生消息的Worker线程
ConsoleMessage::ConsoleMessage(mojom::blink::ConsoleMessageLevel level,
                               const String& message,
                               SourceLocation* location,
                               WorkerThread* worker_thread)
    : ConsoleMessage(mojom::blink::ConsoleMessageSource::kWorker,
                     level,
                     message,
                     location) {
  // 存储Worker线程唯一标识（用于DevTools区分主线程和Worker消息）
  worker_id_ =
      IdentifiersFactory::IdFromToken(worker_thread->GetDevToolsWorkerToken());
}

// 构造函数3：将WebConsoleMessage转换为Blink内部ConsoleMessage
// 参数说明：
// - message：WebConsoleMessage对象（Chromium层消息格式）
// - local_frame：关联的页面帧
ConsoleMessage::ConsoleMessage(const WebConsoleMessage& message,
                               LocalFrame* local_frame)
    : ConsoleMessage(message.nodes.empty()
                         ? mojom::blink::ConsoleMessageSource::kOther
                         : mojom::blink::ConsoleMessageSource::kRecommendation,
                     message.level,
                     message.text,
                     MakeGarbageCollected<SourceLocation>(message.url,
                                                          String(),
                                                          message.line_number,
                                                          message.column_number,
                                                          nullptr)) {
  // 如果关联页面帧存在，存储消息涉及的DOM节点ID列表
  if (local_frame) {
    Vector<DOMNodeId> nodes;
    for (const WebNode& web_node : message.nodes)
      nodes.push_back(web_node.GetDomNodeId());
    SetNodes(local_frame, std::move(nodes));
  }
}

// 基础构造函数：初始化消息核心属性
// 参数说明：
// - source：消息来源分类
// - level：消息级别
// - message：消息文本内容
// - location：消息来源的代码位置信息
ConsoleMessage::ConsoleMessage(mojom::blink::ConsoleMessageSource source,
                               mojom::blink::ConsoleMessageLevel level,
                               const String& message,
                               SourceLocation* location)
    : source_(source),          // 消息来源（如Worker/网络/其他）
      level_(level),            // 消息级别（错误/警告/日志等）
      message_(message),        // 消息文本内容
      location_(location),      // 消息来源的代码位置（URL/行号/列号）
      timestamp_(base::Time::Now().InMillisecondsFSinceUnixEpoch()),  // 消息产生时间戳
      frame_(nullptr) {         // 关联的页面帧（初始为空）
  DCHECK(location_);  // 确保代码位置信息不为空
}

// 析构函数：默认实现
ConsoleMessage::~ConsoleMessage() = default;

// 获取消息来源的代码位置（包含URL/行号等信息）
SourceLocation* ConsoleMessage::Location() const {
  return location_.Get();
}

// 获取关联的网络请求ID（用于关联网络面板中的请求）
const String& ConsoleMessage::RequestIdentifier() const {
  return request_identifier_;
}

// 获取消息产生的时间戳（毫秒级，用于控制台排序）
double ConsoleMessage::Timestamp() const {
  return timestamp_;
}

// 获取消息来源分类
ConsoleMessage::Source ConsoleMessage::GetSource() const {
  return source_;
}

// 获取消息级别（错误/警告等）
ConsoleMessage::Level ConsoleMessage::GetLevel() const {
  return level_;
}

// 获取消息文本内容
const String& ConsoleMessage::Message() const {
  return message_;
}

// 获取关联的Worker线程ID（用于区分主线程和Worker消息）
const String& ConsoleMessage::WorkerId() const {
  return worker_id_;
}

// 获取关联的页面帧（确保只返回已连接的帧）
LocalFrame* ConsoleMessage::Frame() const {
  // 不引用已分离的帧
  if (frame_ && frame_->Client())
    return frame_.Get();
  return nullptr;
}

// 获取消息关联的DOM节点ID列表
Vector<DOMNodeId>& ConsoleMessage::Nodes() {
  return nodes_;
}

// 设置消息关联的DOM节点和页面帧
void ConsoleMessage::SetNodes(LocalFrame* frame, Vector<DOMNodeId> nodes) {
  frame_ = frame;    // 关联页面帧
  nodes_ = std::move(nodes);  // 存储DOM节点ID列表
}

// 获取消息分类（如安全/性能相关，可选）
const std::optional<mojom::blink::ConsoleMessageCategory>&
ConsoleMessage::Category() const {
  return category_;
}

// 设置消息分类
void ConsoleMessage::SetCategory(
    mojom::blink::ConsoleMessageCategory category) {
  category_ = category;
}

// 垃圾回收追踪方法：标记需要被GC追踪的对象引用
void ConsoleMessage::Trace(Visitor* visitor) const {
  visitor->Trace(frame_);    // 追踪页面帧引用
  visitor->Trace(location_); // 追踪代码位置引用
}

}  // namespace blink
```

### 功能总结

`ConsoleMessage` 是 Blink 引擎中控制台消息的标准化封装类，核心功能如下：

1. 统一消息格式
    

2. 整合 Blink 中不同来源（主线程、Worker 线程、网络模块等）、不同类型（日志、错误、警告等）的控制台消息，提供一致的数据结构。
    

3. 存储完整元信息
    

4. 包含消息的文本内容、来源分类、级别、时间戳、代码位置（URL/行号）、关联的网络请求ID、Worker线程ID、DOM节点等全量信息，为后续处理（如显示到DevTools）提供数据支撑。
    

5. 适配多场景消息
    

6. 通过多个构造函数，分别处理网络请求消息、Worker线程消息、Chromium层消息（`WebConsoleMessage`）等不同场景，确保各类消息都能被正确封装。
    

7. 支撑DevTools交互
    

8. 提供的代码位置、DOM节点关联、线程ID等信息，是DevTools实现“点击日志跳转源码”“高亮关联DOM元素”“区分主线程/Worker消息”等功能的基础。
    

9. 兼容垃圾回收
    

10. 通过 `Trace` 方法支持Blink的垃圾回收机制，避免内存泄漏。