# AiPlus Compact Reminder
[English README](README.md)

## 痛点

如果你跑过长时间 Codex / Claude Code / OpenCode 会话，下面三件事大概都遇到过：

1. **经常忘了 compact。** 你正写一个 feature，agent 在出代码，没人盯 token 表。等谁注意到的时候，context 已经溢出，agent 也已经开始忘记早期的需求了。
2. **不知道什么时候是合适的 compact 时机。** 任务中间 compact，半成品状态丢了。任务结束才 compact，错过了"换张白纸继续干"的窗口。**没有一个清楚的"安全交接点"信号**。
3. **直接 compact 会破坏任务交接和连续性。** 没准备就 compact，handoff 没了、decision log 截断了。Resume 之后 agent 像失忆，问的都是已经回答过的问题。你得重新解释任务、重新建立约束、从头重建上下文。

## 我们的解决方案

AiPlus Compact Reminder 把 compact 从"惊慌操作"变成"计划好的操作"。

**它在合适的时候提醒你 compact。** 不是在 token 表已经爆了之后。提醒信号结合了 token 阈值 + 任务交接点检测——所以建议会落在工作的自然缝隙处，不是在一句话讲到一半。

**它在 compact 之前自动准备结构化交接：**

- `current-handoff` —— 你正在做什么、接下来做什么
- `decision-log` —— 做过的决策和原因（让 resume 接到的是**推理过程**，不只是代码）
- `agent-state-ledger` —— 当前任务状态、未决问题、计划动作
- `evidence-ledger` —— 支持上下文和参考

**它在 compact 之后自动续上。** Capsule 自动校验 + 自动提取。Decision ledger 被恢复，agent 从离开的地方继续——**带着对之前所有选择的完整记忆**，不是从零开始。

如果 capsule 缺失或损坏，会优雅退到 legacy handoff 格式。**你不会被卡死**。

## 入门

如果你已经装了 AiPlus：

```bash
aiplus install
cd MyProject
aiplus compact init
```

然后是你期待的子命令：

```bash
aiplus compact remind        # 现在适不适合 compact
aiplus compact prepare       # 建 handoff + capsule
aiplus compact checkpoint    # compact 之前验一下就绪
aiplus compact resume        # compact 之后从 capsule 续上
aiplus compact savings       # 这次省了多少 token 和钱
```

或者作为独立模块：

```bash
git clone https://github.com/izhiwen/aiplus-compact-reminder.git
cd aiplus-compact-reminder
```

CLI 子命令 `aiplus compact` 保持不变——**你的肌肉记忆还能用**。

## 仓库结构

- `core/templates/` —— handoff 模板（current-handoff, decision-log, agent-state-ledger, evidence-ledger）
- `core/schemas/` —— context-capsule 和 reminder state 的 JSON schema
- `core/docs/protocol.md` —— 完整 compact 协议参考
- `adapters/codex/` —— Codex adapter 和 `compact` 命令
- `adapters/claude-code/` —— Claude Code adapter 和命令
- `adapters/opencode/` —— OpenCode adapter 和命令

## 安全边界

Compact Reminder 是**准备工具**，不是 autopilot。它**不会**：

- 帮你点 UI 控件或自动调 `/compact`（compact 还是你触发）
- 唤醒正在等用户输入的 host runtime
- 检测所有可能的 secret 或私有模式（只做结构化检查）
- 替代人工对 Owner gate 的评审
- 上传 prompt / checkpoint / savings 数据

## 更多

- 主平台：[aiplus](https://github.com/izhiwen/aiplus)
- 下次发布前要跟进的事：
  [v0.5.2 known gaps](https://github.com/izhiwen/aiplus/blob/main/docs/roadmap/v0.5.2-known-gaps.md)

## License

[Apache-2.0](LICENSE)
