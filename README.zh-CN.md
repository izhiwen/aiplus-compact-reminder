# Compact Reminder
[English](README.md)

## The Problem

你的会话 stalled，因为你忘记运行 `compact`，直到上下文窗口已经溢出。那时，agent 已经开始遗忘早期的需求，你只能在慌乱中执行 compact。

什么时候是 compact 的好时机，这一点并不清晰。任务中途 compact 意味着状态丢失；任务结束后再 compact 意味着浪费了机会。你没有安全交接点的信号。

未经准备的直接 compact 会破坏任务交接和连续性。你的 handoff 丢失，decision log 被截断，agent 在恢复后感到失忆。你必须重新解释任务、重新建立约束、并从记忆中重建一切。

## The Solution

Compact Reminder 会在适当的时机主动提醒你 compact。它结合 token 阈值与任务交接点检测，让你在正确的时刻执行 compact，既不太早也不太晚。

在 compact 之前，它会自动准备结构化的 handoff：

- **current-handoff** — 你正在做什么以及接下来做什么
- **decision-log** — 已做出的决策及原因
- **agent-state-ledger** — 当前任务状态、待解决问题、下一步行动
- **evidence-ledger** — 支持性上下文和参考信息

在 compact 之后，它通过 capsule 自动恢复。decision ledger 被提取并恢复，因此 agent 可以从它离开的确切位置继续，并完全了解先前的决策和状态。如果 capsule 丢失或损坏，它会优雅地回退到 legacy handoff 格式。

## Quick Start

### Bundled（推荐）

如果你已经在使用 AiPlus：

```bash
aiplus install
cd MyProject
aiplus compact init
```

然后使用你已经熟悉的子命令：

```bash
aiplus compact remind       # 检查是否建议 compact
aiplus compact prepare      # 构建上下文 capsule 和 handoff
aiplus compact checkpoint   # 在 compact 前验证就绪状态
aiplus compact resume       # 在 compact 后从 capsule 恢复上下文
aiplus compact savings      # 显示 token 和成本节省
```

### Standalone

```bash
git clone https://github.com/izhiwen/aiplus-compact-reminder.git
cd aiplus-compact-reminder
```

CLI 子命令 `aiplus compact` 保持不变，以维持肌肉记忆。

## What's Inside

- `core/templates/` — 结构化 handoff 模板（current-handoff、decision-log、
  agent-state-ledger、evidence-ledger）
- `core/schemas/` — 用于 context-capsule 和状态验证的 JSON schema
- `core/docs/protocol.md` — 完整的 compact 协议参考
- `adapters/codex/` — Codex 适配器和 compact 命令
- `adapters/claude-code/` — Claude Code 适配器和命令
- `adapters/opencode/` — OpenCode 适配器和命令
- `core/scripts/compactctl.mjs` — 遗留 Node 辅助脚本（归档，仅用于兼容性测试）

## Safety Boundaries

Compact Reminder 不会：

- 替你点击 UI 控件或调用 `/compact`
- 唤醒正在等待用户输入的主机运行时
- 检测所有可能的秘密或隐私模式（仅进行结构性检查）
- 替代人工审查 Owner gates
- 上传 prompts、checkpoints 或 savings 数据

## More Info

访问 [AiPlus 主仓库](https://github.com/izhiwen/aiplus) 了解完整平台。

当前已知缺口和计划中的工作：
[v0.5.2 known gaps](https://github.com/izhiwen/aiplus/blob/main/docs/roadmap/v0.5.2-known-gaps.md)

## License

[Apache-2.0](LICENSE)
