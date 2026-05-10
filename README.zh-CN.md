# AiPlus Auto Compact

[English README](README.md)

## 问题所在

Codex session 跑了三个小时。context window 快满了，agent 开始忘记对话一开始的需求。你手动触发 compact，丢了一半上下文。compact 后 agent 问"我们刚才在做什么？"你得重新解释任务、重新建立约束、凭记忆重建一切。每次长 session 都发生这种事，每次都在丢时间和上下文。

## 它能做什么

AiPlus Auto Compact 在 compact 发生前准备结构化交接。它把三样东西捕获进 checksum 验证的 capsule：

1. **Decision log** — 做了哪些决定及原因
2. **Agent state ledger** — 当前任务状态、待解决问题和下一步行动
3. **Evidence ledger** — 支撑上下文和参考

compact 后，`aiplus compact resume` 读取 capsule 并自动恢复上下文。agent 从断点继续，完全了解之前的决定和状态。如果 capsule 缺失或损坏，它会优雅地回退到 legacy handoff 格式。

系统还监控 context 使用情况，主动建议 compact 时机。它估算 token 和成本节省，让你知道 compact 是否值得打断。

## 安装

已安装 AiPlus：

```bash
cd MyProject
aiplus install codex        # 或: claude-code, opencode, all
```

然后在 agent session 里说：

```text
帮我准备 compact
```

或使用 standalone 源码：

```bash
git clone https://github.com/izhiwen/aiplus-auto-compact.git
cd aiplus-auto-compact
```

## Runtime 支持

| Runtime | 安装命令 | Compact 支持 |
|---------|---------|-------------|
| Codex | `aiplus install codex` | 提醒、checkpoint 和 resume |
| Claude Code | `aiplus install claude-code` | Reviewed hooks 和 commands |
| OpenCode | `aiplus install opencode` | 项目级 command 工作流 |
| 全部 | `aiplus install all` | 三个 runtime |

## 工作原理

**Compact 前：**

```bash
aiplus compact remind       # 检查是否建议 compact
aiplus compact prepare      # 构建 context capsule 和 handoff
aiplus compact checkpoint   # 验证 readiness
```

agent 会报告 compact 是否安全、被阻塞或需要准备。

**Compact 后：**

```bash
aiplus compact resume       # 从 capsule 恢复
aiplus compact savings      # 显示 token 和成本节省
```

自然续接用语如 `继续`、`resume`、`go on`、`接着做` 也可以触发恢复。

## 仓库结构

- `core/templates/` — 含角色感知章节的 compact handoff 模板（Session Role、Workflow Level、Output Contract）
- `core/schemas/` — context-capsule 和 reminder-state 验证的 JSON schema
- `core/docs/protocol.md` — 完整 compact 协议参考
- `adapters/codex/` — Codex compact 命令 plugin assets
- `adapters/claude-code/` — Claude Code 命令和可选 hooks
- `adapters/opencode/` — OpenCode compact 工作流命令和 prompts
- `examples/` — 三个 runtime 的 synthetic examples
- `core/scripts/compactctl.mjs` — 旧版 Node helper（已归档，仅用于兼容性测试）

## 命令

```bash
# 准备
aiplus compact remind       # 检查 compact 建议
aiplus compact prepare      # 构建 capsule 和 handoff
aiplus compact checkpoint   # 验证 readiness

# Compact 后
aiplus compact resume       # 从 capsule 恢复上下文
aiplus compact savings      # 显示 token 和成本节省
```

## 安全

AiPlus Auto Compact 不：
- 点击 UI 控件或替你调用 `/compact`
- 唤醒等待用户输入的 host runtime
- 检测所有可能的 secret 或私人模式（仅 structural checks）
- 替代 Owner gates 的人工 review
- 上传 prompts、checkpoints 或 savings 数据

## 更多信息

见 [主 AiPlus 仓库](https://github.com/izhiwen/aiplus) 了解完整平台。

当前缺口和计划工作：[v0.5.2 known gaps](https://github.com/izhiwen/aiplus/blob/main/docs/roadmap/v0.5.2-known-gaps.md)。

## License

[Apache-2.0](LICENSE)
