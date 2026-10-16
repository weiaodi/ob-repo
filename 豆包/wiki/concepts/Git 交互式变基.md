---
type: concept
created: 2026-10-16
---
# Git 交互式变基

git rebase -i 通过改写提交序列来整理历史：把同主题 commit 前缀改为 squash（保留合并后 message）或 fixup（丢弃 message），可让提交历史线性清晰，但会改变哈希，公共分支禁用。

## 出现在
- [[豆包/wiki/summaries/编程与技术/开发工具与效率|编程与技术 · 开发工具与效率]]
