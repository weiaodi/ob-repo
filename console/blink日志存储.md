---
uid: 20250825165401
tags: 
created: 2025-08-25-16-54-01
updated: 2025-08-25-16-54-01
---
blink日志存储
[https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/renderer/core/inspector/console_message_storage.cc;l=19;bpv=0;bpt=1](https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/renderer/core/inspector/console_message_storage.cc;l=19;bpv=0;bpt=1)

```c
// Copyright 2016 The Chromium Authors
// Use of this source code is governed by a BSD-style license that can be
// found in the LICENSE file.

#include "third_party/blink/renderer/core/inspector/console_message_storage.h"

#include "base/notreached.h"
#include "base/trace_event/trace_event.h"
#include "third_party/blink/renderer/core/inspector/console_message.h"
#include "third_party/blink/renderer/core/probe/core_probes.h"
#include "third_party/blink/renderer/platform/instrumentation/tracing/traced_value.h"

namespace blink {

// 日志存储的最大容量限制（最多存储1000条日志）
// 超过此数量时，会删除最早的日志并记录过期计数
static const unsigned kMaxConsoleMessageCount = 1000;

namespace {

// 将日志来源枚举转换为字符串（用于日志追踪和调试）
// 例如：kJavaScript → "JS"，kNetwork → "Network"
const char* MessageSourceToString(mojom::ConsoleMessageSource source) {
  switch (source) {
    case mojom::ConsoleMessageSource::kXml:
      return "XML";
    case mojom::ConsoleMessageSource::kJavaScript:
      return "JS";
    case mojom::ConsoleMessageSource::kNetwork:
      return "Network";
    case mojom::ConsoleMessageSource::kConsoleApi:
      return "ConsoleAPI";
    case mojom::ConsoleMessageSource::kStorage:
      return "Storage";
    case mojom::ConsoleMessageSource::kRendering:
      return "Rendering";
    case mojom::ConsoleMessageSource::kSecurity:
      return "Security";
    case mojom::ConsoleMessageSource::kOther:
      return "Other";
    case mojom::ConsoleMessageSource::kDeprecation:
      return "Deprecation";
    case mojom::ConsoleMessageSource::kWorker:
      return "Worker";
    case mojom::ConsoleMessageSource::kViolation:
      return "Violation";
    case mojom::ConsoleMessageSource::kIntervention:
      return "Intervention";
    case mojom::ConsoleMessageSource::kRecommendation:
      return "Recommendation";
  }
  NOTREACHED(); // 处理未定义的枚举值，避免编译器警告
}

// 为日志创建可追踪的值对象（用于Chrome性能追踪系统）
// 包含日志内容和来源URL等关键信息
std::unique_ptr<TracedValue> MessageTracedValue(ConsoleMessage* message) {
  auto value = std::make_unique<TracedValue>();
  value->SetString("content", message->Message()); // 日志内容
  if (!message->Location()->Url().empty()) {
    value->SetString("url", message->Location()->Url()); // 日志来源URL
  }
  return value;
}

// 对错误级别日志进行追踪记录（集成到Chrome的TRACE_EVENT系统）
// 用于性能分析和错误监控（如Telemetry指标收集）
void TraceConsoleMessageEvent(ConsoleMessage* message) {
  // 注意：修改此函数需要同步调整Catapult/Telemetry的相关指标
  // 参考：https://crbug.com/880432
  if (message->GetLevel() == ConsoleMessage::Level::kError) {
    TRACE_EVENT_INSTANT2("blink.console", "ConsoleMessage::Error",
                         TRACE_EVENT_SCOPE_THREAD, 
                         "source", MessageSourceToString(message->GetSource()), // 错误来源
                         "message", MessageTracedValue(message)); // 错误详情
  }
}
}  // anonymous namespace

// 构造函数：初始化日志存储容器和过期计数
ConsoleMessageStorage::ConsoleMessageStorage() : expired_count_(0) {}

// 添加日志到存储容器的核心方法
// 参数：
// - context：日志产生的执行上下文（如页面Document、Worker）
// - message：待存储的日志对象
// - discard_duplicates：是否启用去重（避免相同内容的日志重复存储）
// 返回值：是否成功添加日志（去重时可能返回false）
bool ConsoleMessageStorage::AddConsoleMessage(ExecutionContext* context,
                                              ConsoleMessage* message,
                                              bool discard_duplicates) {
  DCHECK(messages_.size() <= kMaxConsoleMessageCount); // 断言：确保存储数量不超过上限

  // 去重逻辑：如果启用去重，检查是否已有相同内容的日志
  if (discard_duplicates) {
    for (auto& console_message : messages_) {
      if (message->Message() == console_message->Message())
        return false; // 找到重复日志，不添加
    }
  }

  // 对错误级别日志进行追踪（集成到Chrome的性能追踪系统）
  TraceConsoleMessageEvent(message);

  // 通知Blink内部探针：有新日志添加（触发InspectorLogAgent等模块的回调）
  probe::ConsoleMessageAdded(context, message);

  // 容量控制：如果已达最大存储量，先删除最早的日志并增加过期计数
  if (messages_.size() == kMaxConsoleMessageCount) {
    ++expired_count_; // 记录过期日志数量
    messages_.pop_front(); // 删除最旧的日志（队列头部）
  }

  // 将新日志添加到存储容器尾部
  messages_.push_back(message);
  return true;
}

// 清空所有日志和过期计数（对应DevTools控制台的"清空"功能）
void ConsoleMessageStorage::Clear() {
  messages_.clear(); // 清空日志容器
  expired_count_ = 0; // 重置过期计数
}

// 获取当前存储的日志数量
wtf_size_t ConsoleMessageStorage::size() const {
  return messages_.size();
}

// 根据索引获取指定日志（用于InspectorLogAgent读取历史日志）
ConsoleMessage* ConsoleMessageStorage::at(wtf_size_t index) const {
  return messages_[index].Get();
}

// 获取已过期的日志数量（用于InspectorLogAgent提示用户）
int ConsoleMessageStorage::ExpiredCount() const {
  return expired_count_;
}

// 垃圾回收追踪方法：通知V8垃圾回收器需要追踪messages_中的对象
// 避免日志对象被误回收
void ConsoleMessageStorage::Trace(Visitor* visitor) const {
  visitor->Trace(messages_);
}

}  // namespace blink
```