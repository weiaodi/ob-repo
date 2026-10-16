---
type: summary
theme: 编程与技术
source: 豆包/raw/编程与技术/AI、Agent与智能体.md
created: 2026-10-16
---
# AI、Agent与智能体

来源：[[豆包/raw/编程与技术/AI、Agent与智能体]]

> 这组对话集中在 AI Agent 的架构拆解与工程落地：Agent 分类与五大核心模块、Agent Harness 生态选型、SDD 规范驱动开发工具链、WebMCP 浏览器原生协议、Org as Code 组织形态，以及 Kimi / Stitch 等产品与竞品情报。夹杂大厂程序员 AI 焦虑文案和若干配图请求（低信息量部分）。

## 核心要点
- AI Agent 五大核心模块：LLM 基座（大脑）、提示词工程与角色编排、记忆系统（短期上下文 + 向量库长期记忆）、任务规划与拆解（链式/分层/ReAct）、工具调用与行动执行（Function Calling）、反思迭代。一句话：Agent = 大模型大脑 + 记忆 + 规划 + 工具 + 反思。
- Agent 三种分类维度：按能力（单任务 / 多任务通用 / 垂直专属）、按架构（反射式 / 规划式 / 记忆型 / 工具调用型）、按运行形态（会话式 / 后台自动化工作流 / 多智能体协作）。
- Agent Harness 生态：OpenHarness（港大开源，1.17 万行 Python、覆盖 Claude Code 约 98% 工具能力）、jcode（Rust，启动 14ms、单会话 28MB）、RuFlow（蜂巢式多 Agent）、OpenClaw / SemaClaw（通用个人助手 + PermissionBridge 安全层）、Chorus（逆向对话、人在回路）；OpenAI 开源 Codex Harness 仓库为 github.com/openai/codex。
- SDD（Spec-Driven Development，规范驱动开发）= 先把需求和方案写完整再让 AI 写代码，避免 AI 理解偏差与反复返工；配套工具按重量排序：speckit（追问需求、产出用户故事/验收标准/任务清单）→ superpowers（轻量技能插件）→ Claude Code plan mode（原生、只读不改文件），openkit 属工程化编排、尚未落地。
- WebMCP 是 W3C 推动的浏览器原生标准，让网页主动暴露契约化工具给 Agent，替代 DOM 解析与坐标点击。三层桥接：Web 应用层（Provider）/ 浏览器内核层（Broker，`navigator.modelContext`）/ AI Agent 层（Consumer）；双轨 API 为声明式表单属性与命令式 `registerTool`；安全靠 Ed25519 签名、元素级 CSRF、执行沙箱、权限粒度与内部 URL 脱敏；`page.wmcp` 元数据文件可把解析 token 降低 90% 以上。
- Org as Code（公司即 GitHub）：Repo 代替部门、Issue 是任务、PR 是提案评审、Commit 是变更记录，全资产版本化且机器可读；InnerSource 是协作方法论，Company as GitHub / Org as Code 是产品形态愿景。现实痛点是 Notion、飞书文档、多维表缺少原生版本变更流、PR 评审与细粒度变更溯源。
- 产品与赛道情报：Google Stitch（Gemini 3 驱动，文生 UI / 图生 UI / 多屏原型 / 一键导出 React-Tailwind 代码，免费版每月约 350 次）；月之暗面产品矩阵（Kimi Chat / Kimi Work / Kimi Code / Moonshot API / Kimi Claw + K2–K3 开源基座）；AI 文档与多维表竞品全景（Airtable、Notion Database、Coda、Baserow、NocoDB、Anytype、Capacities、Row Zero、Linear 等）。
- 易混概念澄清：Codex + CC 中的 CC = Claude Code，`codex-plugin-cc` 是在 Claude Code 内调用 Codex 做 `review / adversarial-review / rescue`，用跨模型规避同模型思维盲区；RPA 是按固定规则模拟人操作界面的"软件机器人"（非侵入、低代码、7×24），与能判断的智能 RPA（OCR/NLP）不是一回事。

## 涉及概念
[[AI Agent]]、[[Agent Harness]]、[[MCP（模型上下文协议）]]、[[WebMCP]]、[[SDD（规范驱动开发）]]、[[Org as Code（组织即代码）]]、[[多维表]]、[[RPA（机器人流程自动化）]]

## 值得回看的问答
- **Agent 在 AI 领域一般分为什么东西？然后通过几个技术拆分出来？**：分类按能力/架构/运行形态三个维度；技术拆成 LLM 基座、提示词与角色编排、记忆系统、任务规划、工具调用、反思迭代六块，极简公式是"大模型大脑 + 记忆 + 任务规划 + 工具调用 + 自我反思"。
- **WebMCP 的技术实现细节**：六大模块完整拆解——三层桥接架构、声明式与命令式双轨 API、SSE + JSON-RPC 2.0 调用流程、Ed25519 签名与元素级 CSRF 与沙箱隔离、`page.wmcp` 元数据文件、浏览器原生实现限制（仅 HTTPS、每页最多 100 个工具）。
- **目前有除了 cloud code 之外的 harness 工程吗？**：有，按四个方向选型——开源轻量可定制选 OpenHarness，极致性能低内存选 jcode，多 Agent 复杂协作选 RuFlow，通用个人助手 + 安全选 OpenClaw/SemaClaw。
- **SDD 配套工具很多不强制，部分工具有哪些？**：speckit（严谨、文档量大）、Claude Code plan mode（轻量、只做方案不动文件）、super power（介于两者之间的日常中等复杂度）、openkit（整套 AI 工作流托管，暂观望）。
- **ai 时代把公司变成 github 的形式这个是什么？他具体的产品化有什么？**：定义是 Org as Code；产品化分六层——企业 Git 平台底座、文档 + 代码打通、一体化工作空间（Coda/Capacities/Linear）、Org-as-Code 治理层、AI 评审与 MCP 服务层组件、存量拼接 vs 一体化自研两条商业路线；市场空白是"多维表 + Markdown 文档 + Git 版本 + PR 评审 + Agent 友好 API"一体化平台。
- **codex 的 cc 是什么？**：CC = Claude Code，不是 Creative Commons 也不是 cc-switch；`codex-plugin-cc` 让 Claude Code 写、Codex 独立审核，规避同模型盲区。
