---
uid: 20250825165404
tags: 
created: 2025-08-25-16-54-04
updated: 2025-08-25-16-54-04
---
[https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/renderer/core/inspector/inspector_log_agent.cc;bpv=0;bpt=1](https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/renderer/core/inspector/inspector_log_agent.cc;bpv=0;bpt=1)

```c++
// Copyright 2016 The Chromium Authors
// Use of this source code is governed by a BSD-style license that can be
// found in the LICENSE file.

namespace blink {

namespace {

// 将Blink内部的日志来源枚举转换为DevTools协议的日志来源字符串
// 例如：kJavaScript → "javascript"，供前端识别日志类型
String MessageSourceValue(mojom::blink::ConsoleMessageSource source) {
  DCHECK(source != mojom::blink::ConsoleMessageSource::kConsoleApi);
  switch (source) {
    case mojom::blink::ConsoleMessageSource::kXml:
      return protocol::Log::LogEntry::SourceEnum::Xml;
    case mojom::blink::ConsoleMessageSource::kJavaScript:
      return protocol::Log::LogEntry::SourceEnum::Javascript;
    case mojom::blink::ConsoleMessageSource::kNetwork:
      return protocol::Log::LogEntry::SourceEnum::Network;
    case mojom::blink::ConsoleMessageSource::kStorage:
      return protocol::Log::LogEntry::SourceEnum::Storage;
    case mojom::blink::ConsoleMessageSource::kRendering:
      return protocol::Log::LogEntry::SourceEnum::Rendering;
    case mojom::blink::ConsoleMessageSource::kSecurity:
      return protocol::Log::LogEntry::SourceEnum::Security;
    case mojom::blink::ConsoleMessageSource::kOther:
      return protocol::Log::LogEntry::SourceEnum::Other;
    case mojom::blink::ConsoleMessageSource::kDeprecation:
      return protocol::Log::LogEntry::SourceEnum::Deprecation;
    case mojom::blink::ConsoleMessageSource::kWorker:
      return protocol::Log::LogEntry::SourceEnum::Worker;
    case mojom::blink::ConsoleMessageSource::kViolation:
      return protocol::Log::LogEntry::SourceEnum::Violation;
    case mojom::blink::ConsoleMessageSource::kIntervention:
      return protocol::Log::LogEntry::SourceEnum::Intervention;
    case mojom::blink::ConsoleMessageSource::kRecommendation:
      return protocol::Log::LogEntry::SourceEnum::Recommendation;
    default:
      return protocol::Log::LogEntry::SourceEnum::Other;
  }
}

// 将Blink内部的日志级别枚举转换为DevTools协议的日志级别字符串
// 例如：kError → "error"，控制前端日志的显示样式（红色错误、黄色警告等）
String MessageLevelValue(mojom::blink::ConsoleMessageLevel level) {
  switch (level) {
    case mojom::blink::ConsoleMessageLevel::kVerbose:
      return protocol::Log::LogEntry::LevelEnum::Verbose;
    case mojom::blink::ConsoleMessageLevel::kInfo:
      return protocol::Log::LogEntry::LevelEnum::Info;
    case mojom::blink::ConsoleMessageLevel::kWarning:
      return protocol::Log::LogEntry::LevelEnum::Warning;
    case mojom::blink::ConsoleMessageLevel::kError:
      return protocol::Log::LogEntry::LevelEnum::Error;
  }
  return protocol::Log::LogEntry::LevelEnum::Info;
}

// 将Blink内部的日志类别枚举转换为DevTools协议的类别字符串
// 例如：Cors → "cors"，用于前端对日志进行分类筛选
String MessageCategoryValue(mojom::blink::ConsoleMessageCategory category) {
  switch (category) {
    case mojom::blink::ConsoleMessageCategory::Cors:
      return protocol::Log::LogEntry::CategoryEnum::Cors;
  }
  return WTF::g_empty_string;
}

}  // anonymous namespace

// 构造函数：初始化日志代理的核心依赖
// - storage：日志存储容器（ConsoleMessageStorage），用于读取和清理日志
// - performance_monitor：性能监控器，用于接收性能违规事件（如长任务）
// - v8_session：V8调试会话，用于处理日志中关联的JS对象/DOM节点
InspectorLogAgent::InspectorLogAgent(
    ConsoleMessageStorage* storage,
    PerformanceMonitor* performance_monitor,
    v8_inspector::V8InspectorSession* v8_session)
    : storage_(storage),
      performance_monitor_(performance_monitor),
      v8_session_(v8_session),
      enabled_(&agent_state_, /*default_value=*/false),  // 日志代理启用状态（默认关闭）
      violation_thresholds_(&agent_state_, -1.0) {}      // 性能违规监控的阈值配置

InspectorLogAgent::~InspectorLogAgent() = default;

// 垃圾回收追踪：通知V8垃圾回收器需要追踪的成员变量
// 避免storage_、performance_monitor_等被误回收
void InspectorLogAgent::Trace(Visitor* visitor) const {
  visitor->Trace(storage_);
  visitor->Trace(performance_monitor_);
  InspectorBaseAgent::Trace(visitor);
  PerformanceMonitor::Client::Trace(visitor);
}

// 状态恢复：当DevTools重新连接时，恢复之前的日志代理状态
// 包括重新启用日志监听和性能违规监控
void InspectorLogAgent::Restore() {
  if (!enabled_.Get())
    return;
  InnerEnable();  // 重新启用日志监听
  if (violation_thresholds_.IsEmpty())
    return;
  // 重新订阅性能违规监控
  auto settings = std::make_unique<protocol::Array<ViolationSetting>>();
  for (const WTF::String& key : violation_thresholds_.Keys()) {
    settings->emplace_back(ViolationSetting::create()
                               .setName(key)
                               .setThreshold(violation_thresholds_.Get(key))
                               .build());
  }
  startViolationsReport(std::move(settings));
}

// 核心方法：处理新增的控制台日志，转换格式并发送到DevTools前端
// 当ConsoleMessageStorage有新日志时，会触发此回调
void InspectorLogAgent::ConsoleMessageAdded(ConsoleMessage* message) {
  DCHECK(enabled_.Get());  // 仅当日志代理启用时处理

  // 1. 创建DevTools协议的LogEntry对象，填充基本信息
  std::unique_ptr<protocol::Log::LogEntry> entry =
      protocol::Log::LogEntry::create()
          .setSource(MessageSourceValue(message->GetSource()))  // 日志来源（JS/网络等）
          .setLevel(MessageLevelValue(message->GetLevel()))    // 日志级别（错误/警告等）
          .setText(message->Message())                         // 日志内容
          .setTimestamp(message->Timestamp())                   // 日志产生时间戳
          .build();

  // 2. 补充日志的代码位置信息（URL、行号、调用栈）
  if (!message->Location()->Url().empty())
    entry->setUrl(message->Location()->Url());  // 日志来源的URL
  // 构建调用栈信息（如console.trace()的调用栈）
  std::unique_ptr<v8_inspector::protocol::Runtime::API::StackTrace>
      stack_trace = message->Location()->BuildInspectorObject();
  if (stack_trace)
    entry->setStackTrace(std::move(stack_trace));
  // 行号（Blink内部行号从1开始，前端从0开始，需减1）
  if (message->Location()->LineNumber())
    entry->setLineNumber(message->Location()->LineNumber() - 1);

  // 3. 补充特殊上下文信息（Worker/网络请求）
  if (message->GetSource() == ConsoleMessage::Source::kWorker &&
      !message->WorkerId().empty()) {
    entry->setWorkerId(message->WorkerId());  // 关联的Worker ID
  }
  if (message->GetSource() == ConsoleMessage::Source::kNetwork &&
      !message->RequestIdentifier().IsNull()) {
    entry->setNetworkRequestId(message->RequestIdentifier());  // 关联的网络请求ID
  }

  // 4. 处理日志中关联的DOM节点（如console.log(element)）
  if (v8_session_ && message->Frame() && !message->Nodes().empty()) {
    ScriptForbiddenScope::AllowUserAgentScript allow_script;  // 允许在此作用域执行脚本
    auto remote_objects = std::make_unique<
        protocol::Array<v8_inspector::protocol::Runtime::API::RemoteObject>>();
    for (DOMNodeId node_id : message->Nodes()) {
      std::unique_ptr<v8_inspector::protocol::Runtime::API::RemoteObject>
          remote_object;
      Node* node = DOMNodeIds::NodeForId(node_id);  // 通过ID获取DOM节点
      if (node) {
        // 将DOM节点转换为DevTools可引用的RemoteObject（供前端定位节点）
        remote_object = ResolveNode(v8_session_, node, "console", std::nullopt);
      }
      if (!remote_object) {
        // 若节点无法解析，创建空对象引用
        remote_object =
            NullRemoteObject(v8_session_, message->Frame(), "console");
      }
      if (remote_object) {
        remote_objects->emplace_back(std::move(remote_object));
      } else {
        // 若无法创建引用，放弃发送此日志（避免前端显示错误）
        return;
      }
    }
    entry->setArgs(std::move(remote_objects));  // 附加节点引用到日志
  }

  // 5. 补充日志类别（如CORS错误）
  if (auto category = message->Category()) {
    entry->setCategory(MessageCategoryValue(*category));
  }

  // 6. 将转换后的日志发送到DevTools前端，并刷新缓冲区
  GetFrontend()->entryAdded(std::move(entry));
  GetFrontend()->flush();
}

// 内部启用逻辑：开始监听日志并加载历史日志
void InspectorLogAgent::InnerEnable() {
  // 注册为日志监听者，当ConsoleMessageStorage有新日志时会触发ConsoleMessageAdded
  instrumenting_agents_->AddInspectorLogAgent(this);

  // 若有过期日志（超过存储上限被删除的），向前端发送提示
  if (storage_->ExpiredCount()) {
    std::unique_ptr<protocol::Log::LogEntry> expired =
        protocol::Log::LogEntry::create()
            .setSource(protocol::Log::LogEntry::SourceEnum::Other)
            .setLevel(protocol::Log::LogEntry::LevelEnum::Warning)
            .setText(StrCat({String::Number(storage_->ExpiredCount()),
                             " log entries are not shown."}))  // 提示内容
            .setTimestamp(0)
            .build();
    GetFrontend()->entryAdded(std::move(expired));
    GetFrontend()->flush();
  }

  // 加载并发送已存储的历史日志（如DevTools刚打开时显示之前的日志）
  for (wtf_size_t i = 0; i < storage_->size(); ++i)
    ConsoleMessageAdded(storage_->at(i));
}

// DevTools协议接口：启用日志代理（前端调用）
// 例如：用户打开DevTools时，前端会调用此方法
protocol::Response InspectorLogAgent::enable() {
  if (enabled_.Get())
    return protocol::Response::Success();
  enabled_.Set(true);
  InnerEnable();   
  return protocol::Response::Success();
}

// DevTools协议接口：禁用日志代理（前端调用）
// 例如：用户关闭DevTools时，前端会调用此方法
protocol::Response InspectorLogAgent::disable() {
  if (!enabled_.Get())
    return protocol::Response::Success();
  enabled_.Clear();
  stopViolationsReport();  // 停止性能违规监控
  // 取消日志监听注册
  instrumenting_agents_->RemoveInspectorLogAgent(this);
  return protocol::Response::Success();
}

// DevTools协议接口：清空控制台日志（前端调用）
// 对应控制台的"清空"按钮
protocol::Response InspectorLogAgent::clear() {
  storage_->Clear();  // 清空日志存储容器
  return protocol::Response::Success();
}

// 将DevTools前端的性能违规名称转换为Blink内部的违规类型
static PerformanceMonitor::Violation ParseViolation(const String& name) {
  if (name == ViolationSetting::NameEnum::DiscouragedAPIUse)
    return PerformanceMonitor::kDiscouragedAPIUse;  // 不推荐的API使用
  if (name == ViolationSetting::NameEnum::LongTask)
    return PerformanceMonitor::kLongTask;            // 长任务（阻塞主线程）
  if (name == ViolationSetting::NameEnum::LongLayout)
    return PerformanceMonitor::kLongLayout;          // 长时间布局（强制回流）
  if (name == ViolationSetting::NameEnum::BlockedEvent)
    return PerformanceMonitor::kBlockedEvent;        // 阻塞事件
  if (name == ViolationSetting::NameEnum::BlockedParser)
    return PerformanceMonitor::kBlockedParser;       // 阻塞解析器
  if (name == ViolationSetting::NameEnum::Handler)
    return PerformanceMonitor::kHandler;             // 事件处理器耗时
  if (name == ViolationSetting::NameEnum::RecurringHandler)
    return PerformanceMonitor::kRecurringHandler;    // 重复事件处理器耗时
  return PerformanceMonitor::kAfterLast;             // 未识别的违规类型
}

// DevTools协议接口：开始性能违规监控（前端调用）
// 例如：用户在DevTools中开启"长任务监控"时调用
protocol::Response InspectorLogAgent::startViolationsReport(
    std::unique_ptr<protocol::Array<ViolationSetting>> settings) {
  if (!enabled_.Get())
    return protocol::Response::ServerError("Log is not enabled");  
  if (!performance_monitor_) {
    return protocol::Response::ServerError(
        "Violations are not supported for this target");   
  }
 
  performance_monitor_->UnsubscribeAll(this);
  violation_thresholds_.Clear();
  // 遍历前端配置的监控项，订阅对应的性能违规事件
  for (const std::unique_ptr<ViolationSetting>& setting : *settings) {
    const WTF::String& name = setting->getName();
    double threshold = setting->getThreshold();   
    PerformanceMonitor::Violation violation = ParseViolation(name);
    if (violation == PerformanceMonitor::kAfterLast)
      continue;   
   
    performance_monitor_->Subscribe(violation, base::Milliseconds(threshold),
                                    this);
    violation_thresholds_.Set(name, threshold);   
  }
  return protocol::Response::Success();
}

// DevTools协议接口：停止性能违规监控（前端调用）
protocol::Response InspectorLogAgent::stopViolationsReport() {
  violation_thresholds_.Clear();  // 清空配置
  if (!performance_monitor_) {
    return protocol::Response::ServerError(
        "Violations are not supported for this target");
  }
  performance_monitor_->UnsubscribeAll(this);  // 取消所有订阅
  return protocol::Response::Success();
}

// 性能监控回调：报告长时间布局（强制回流）违规
void InspectorLogAgent::ReportLongLayout(base::TimeDelta duration) {
  // 构建违规日志内容（如"Forced reflow took 100ms"）
  String message_text = String::Format(
      "Forced reflow while executing JavaScript took %" PRId64 "ms",
      duration.InMilliseconds());
  // 创建Blink内部的ConsoleMessage（来源为kViolation，级别为kVerbose）
  auto* message = MakeGarbageCollected<ConsoleMessage>(
      mojom::blink::ConsoleMessageSource::kViolation,
      mojom::blink::ConsoleMessageLevel::kVerbose, message_text);
  // 转换格式并发送到前端
  ConsoleMessageAdded(message);
}

// 性能监控回调：报告通用性能违规（如长任务、不推荐API使用）
void InspectorLogAgent::ReportGenericViolation(PerformanceMonitor::Violation,
                                               const String& text,
                                               base::TimeDelta time,
                                               SourceLocation* location) {
  // 创建Blink内部的ConsoleMessage，包含违规详情和代码位置
  auto* message = MakeGarbageCollected<ConsoleMessage>(
      mojom::blink::ConsoleMessageSource::kViolation,
      mojom::blink::ConsoleMessageLevel::kVerbose, text, location);
  // 转换格式并发送到前端
  ConsoleMessageAdded(message);
}

}  // namespace blink
```