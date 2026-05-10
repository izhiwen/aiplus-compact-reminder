# AiPlus Auto Compact

[English README](README.md)

## 痛点

Codex session 跑了三个小时，context window 快满了。agent 开始忘记你一开始给的需求。你手动 compact，丢了一半上下文。compact 后 agent 问"我们刚才在做什么？"像什么都没发生过。你得重新解释任务、重新建立约束，还得指望你记得 agent 忘了的所有东西。

## 解决方案

AiPlus Auto Compact 在 compact 发生前准备结构化交接。它把 decision log、agent state ledger 和 evidence ledger 捕获进 checksum 验证的 capsule。compact 后，`aiplus compact resume` 读取 capsule 并自动恢复上下文。agent 从断点继续，不是从零开始。如果 capsule 缺失或损坏，它会优雅地回退到 legacy handoff。

## 快速开始

如果你已安装 AiPlus：

```bash
cd MyProject
aiplus install codex
```

然后在 agent session 里说：

```text
帮我准备 compact
```

或 clone standalone 源码：

```bash
git clone https://github.com/izhiwen/aiplus-auto-compact.git
cd aiplus-auto-compact
```
