# 豆包 Wiki · 参考手册

> ⚠️ 执行规范以 [AGENTS.md](</Users/weiaodi/Desktop/ob- repository/豆包/AGENTS.md>) 为准。本文件仅保留低层次的事实性信息，供快速查阅；两处不一致时以 AGENTS.md 为准。

## 当前结构（2026-10-16 基线）

| 层 | 内容 | 数量 |
|---|---|---|
| raw/ | 豆包对话整理全文（8 主题） | 60 文件，不可变 |
| wiki/summaries/ | 摘要页 | 60 |
| wiki/concepts/ | 概念页 | 209 |
| wiki/syntheses/ | 综合页 | 7 |
| 顶层 | index.md / log.md / AGENTS.md / WIKI.md | — |

## 已知边界备忘

- 概念页按主要来源主题分组（见 index.md）；跨主题概念已归并（统筹基金、文艺复兴、媚俗（kitsch）等）
- `count` 字段 = 对话出现频次估值，仅作权重参考
- raw 中存在用户手机号等隐私（如「生活与其他/生活常识与其他.md」中的提取微信对话），wiki 层已避免收录，raw 层保留
- 同名文件注意带路径链接：`raw/编程与技术/其他杂项.md`、`raw/投资与理财/其他杂项.md`
- 概念名含全角斜杠：`GLTF／GLB`（文件名避免半角 /）
- 对话时点的费率/政策数字一律标「需核验」；个人身体数据结论标「个人化结论」

## 参考

- Karpathy, "LLM Wiki"（gist 442a6bf555914893e9891c11519de94f）：三层架构、ingest/query/lint、index+log 模式
