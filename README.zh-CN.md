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
aiplus install codex        # 或: claude-code, opencode, all
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

## Runtime 支持

| Runtime | Install command | Compact 支持 |
|---------|----------------|-------------|
| Codex | `aiplus install codex` | 提醒和 checkpoint only |
| Claude Code | `aiplus install claude-code` | Reviewed hooks 和 commands |
| OpenCode | `aiplus install opencode` | 项目级 command 工作流 |
| All | `aiplus install all` | 三个 runtime 全部 |

## 内部结构

- `core/templates/` — 含角色感知章节的 compact handoff 模板
- `core/schemas/` — context-capsule 和 reminder-state 的 JSON schema
- `core/docs/protocol.md` — Compact 协议参考
- `adapters/codex/` — Codex plugin 资源
- `adapters/claude-code/` — Claude Code 命令和 hooks
- `adapters/opencode/` — OpenCode 命令和 prompts
- `examples/` — 三个 runtime 的 synthetic examples

## 常用命令

```bash
aiplus compact remind       # 检查是否建议 compact
aiplus compact prepare      # 构建 context capsule 和 handoff
aiplus compact checkpoint   # 验证 readiness
aiplus compact resume       # compact 后恢复
aiplus compact savings      # 显示 token 和成本节省
```

## 安全边界

AiPlus Auto Compact 不：
- 点击 UI 控件或替你调用 `/compact`
- 唤醒等待用户输入的 host runtime
- 检测所有可能的 secret 或私人模式
- 替代 Owner gates 的人工 review
- 上传 prompts、checkpoints 或 savings 数据

## 路线图

见 [主 AiPlus 仓库](https://github.com/izhiwen/aiplus/blob/main/docs/roadmap/v0.5.2-known-gaps.md) 了解当前缺口。

## License

[Apache-2.0](LICENSE)
