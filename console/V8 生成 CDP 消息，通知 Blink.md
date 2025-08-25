---
uid: 20250825170424
tags: 
created: 2025-08-25-17-04-24
updated: 2025-08-25-17-04-24
---


#### [https://source.chromium.org/chromium/chromium/src/+/main:v8/src/inspector/v8-console-agent-impl.cc;bpv=0;bpt=0](https://source.chromium.org/chromium/chromium/src/+/main:v8/src/inspector/v8-console-agent-impl.cc;bpv=0;bpt=0)

V8 引擎内部先完成消息的“生成”，再主动通知 Blink 接收：

- V8 侧核心模块：`v8/src/inspector/v8-console-agent-impl.cc`（生成 Console 域 CDP 消息）；
    
- 逻辑：V8 的 `V8ConsoleAgentImpl` 处理 `console.log` 调用，生成 CDP 协议的 `Console.messageAdded` 事件
    

```

namespace v8_inspector {

namespace ConsoleAgentState {
static const char consoleEnabled[] = "consoleEnabled";
}  // namespace ConsoleAgentState

V8ConsoleAgentImpl::V8ConsoleAgentImpl(
    V8InspectorSessionImpl* session, protocol::FrontendChannel* frontendChannel,
    protocol::DictionaryValue* state)
    : m_session(session),
      m_state(state),
      m_frontend(frontendChannel),  // 初始化CDP协议前端通道（用于发送CDP消息到Blink）
      m_enabled(false) {}

V8ConsoleAgentImpl::~V8ConsoleAgentImpl() = default;

// 处理CDP协议命令：Console.enable
// 启用后将通过CDP协议发送控制台消息
Response V8ConsoleAgentImpl::enable() {
  if (m_enabled) return Response::Success();
  m_state->setBoolean(ConsoleAgentState::consoleEnabled, true);
  m_enabled = true;
  reportAllMessages();  // 启用后立即补发历史消息（通过CDP协议）
  return Response::Success();
}

// 处理CDP协议命令：Console.disable
// 禁用后停止通过CDP协议发送消息
Response V8ConsoleAgentImpl::disable() {
  if (!m_enabled) return Response::Success();
  m_state->setBoolean(ConsoleAgentState::consoleEnabled, false);
  m_enabled = false;
  return Response::Success();
}

// 处理CDP协议命令：Console.clearMessages
// 通过CDP协议通知前端清空消息
Response V8ConsoleAgentImpl::clearMessages() { return Response::Success(); }

void V8ConsoleAgentImpl::restore() {
  if (!m_state->booleanProperty(ConsoleAgentState::consoleEnabled, false))
    return;
  enable();  // 恢复状态时重新启用CDP消息发送
}

// 当有新控制台消息时触发（如console.log调用）
// 核心：通过CDP协议发送消息的入口
void V8ConsoleAgentImpl::messageAdded(V8ConsoleMessage* message) {
  if (m_enabled) 
    reportMessage(message, true);  // 启用状态下，通过CDP协议上报消息
}

bool V8ConsoleAgentImpl::enabled() { return m_enabled; }

// 上报所有历史消息（如DevTools刚连接时）
// 批量通过CDP协议发送历史控制台消息
void V8ConsoleAgentImpl::reportAllMessages() {
  V8ConsoleMessageStorage* storage =
      m_session->inspector()->ensureConsoleMessageStorage(
          m_session->contextGroupId());
  for (const auto& message : storage->messages()) {
    if (message->origin() == V8MessageOrigin::kConsole) {
      if (!reportMessage(message.get(), false)) return;  // 逐条通过CDP发送
    }
  }
}

// 实际执行CDP协议消息发送的核心方法
bool V8ConsoleAgentImpl::reportMessage(V8ConsoleMessage* message,
                                       bool generatePreview) {
  DCHECK_EQ(V8MessageOrigin::kConsole, message->origin());
  // 1. 将V8内部消息转换为CDP协议格式（如Console.messageAdded事件）
  // 包含消息文本、级别、堆栈等标准化字段
  message->reportToFrontend(&m_frontend);
  
  // 2. 立即通过CDP通道发送消息（刷新缓冲区，确保不滞留）
  m_frontend.flush();
  
  return m_session->inspector()->hasConsoleMessageStorage(
      m_session->contextGroupId());
}

}  // namespace v8_inspector
```