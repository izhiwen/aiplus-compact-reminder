# AiPlus Auto Compact

[中文 README](README.zh-CN.md)

## The Pain

You are three hours into a Codex session and the context window is nearly full. The agent starts forgetting the requirements you gave at the beginning. You manually compact and lose half the thread. After compact, the agent asks "what were we working on?" like nothing happened. You have to re-explain the task, re-establish the constraints, and hope you remember everything the agent forgot.

## The Solution

AiPlus Auto Compact prepares a structured handoff before compact happens. It captures the decision log, agent state ledger, and evidence ledger into a checksum-verified capsule. After compact, `aiplus compact resume` reads the capsule and restores context automatically. The agent continues from where it left off, not from zero. If the capsule is missing or malformed, it falls back to the legacy handoff gracefully.

## Quick Start

If you already have AiPlus:

```bash
cd MyProject
aiplus install codex
```

Then in your agent session:

```text
prepare compact
```

Or clone the standalone source:

```bash
git clone https://github.com/izhiwen/aiplus-auto-compact.git
cd aiplus-auto-compact
```
