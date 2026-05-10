# AiPlus Auto Compact

[中文 README](README.zh-CN.md)

## The Problem

You are three hours into a Codex session. The context window is nearly full and the agent starts forgetting the requirements from the beginning of the conversation. You manually trigger compact and lose half the thread. After compact, the agent asks "what were we working on?" You have to re-explain the task, re-establish constraints, and reconstruct everything from memory. This happens every long session, and every time you lose time and context.

## What It Does

AiPlus Auto Compact prepares a structured handoff before compact happens. It captures three things into a checksum-verified capsule:

1. **Decision log** — What decisions were made and why
2. **Agent state ledger** — Current task state, open questions, and next actions
3. **Evidence ledger** — Supporting context and references

After compact, `aiplus compact resume` reads the capsule and restores context automatically. The agent continues from the exact point it left off, with full awareness of prior decisions and state. If the capsule is missing or malformed, it falls back to the legacy handoff format gracefully.

The system also monitors context usage and suggests compact timing proactively. It estimates token and cost savings so you know whether compact is worth the interruption.

## Installation

With AiPlus already installed:

```bash
cd MyProject
aiplus install codex        # or: claude-code, opencode, all
```

Then in your agent session:

```text
prepare compact
```

Or use the standalone source:

```bash
git clone https://github.com/izhiwen/aiplus-auto-compact.git
cd aiplus-auto-compact
```

## Runtime Support

| Runtime | Install Command | Compact Support |
|---------|----------------|-----------------|
| Codex | `aiplus install codex` | Reminder, checkpoint, and resume |
| Claude Code | `aiplus install claude-code` | Reviewed hooks and commands |
| OpenCode | `aiplus install opencode` | Project-local command workflow |
| All | `aiplus install all` | All three runtimes |

## How It Works

**Before compact:**

```bash
aiplus compact remind       # Check if compact is recommended
aiplus compact prepare      # Build context capsule and handoff
aiplus compact checkpoint   # Validate readiness
```

The agent will report whether compact is safe, blocked, or needs preparation.

**After compact:**

```bash
aiplus compact resume       # Restore from capsule
aiplus compact savings      # Show token and cost savings
```

Natural continuation phrases like `continue`, `resume`, `go on`, `接着做` also work.

## Repository Structure

- `core/templates/` — Compact handoff templates with role-aware sections (Session Role, Workflow Level, Output Contract)
- `core/schemas/` — JSON schemas for context-capsule and reminder-state validation
- `core/docs/protocol.md` — Complete compact protocol reference
- `adapters/codex/` — Codex plugin assets for compact commands
- `adapters/claude-code/` — Claude Code commands and optional hooks
- `adapters/opencode/` — OpenCode commands and prompts for compact workflow
- `examples/` — Synthetic examples for all three runtimes
- `core/scripts/compactctl.mjs` — Legacy Node helper (archived, for compatibility tests only)

## Commands

```bash
# Preparation
aiplus compact remind       # Check compact recommendation
aiplus compact prepare      # Build capsule and handoff
aiplus compact checkpoint   # Validate readiness

# After compact
aiplus compact resume       # Restore context from capsule
aiplus compact savings      # Show token and cost savings
```

## Safety

AiPlus Auto Compact does not:
- Click UI controls or call `/compact` for you
- Wake a host runtime that is waiting for user input
- Detect every possible secret or private pattern (structural checks only)
- Replace human review of Owner gates
- Upload prompts, checkpoints, or savings data

## More Information

See the [main AiPlus repository](https://github.com/izhiwen/aiplus) for the complete platform.

Current gaps and planned work: [v0.5.2 known gaps](https://github.com/izhiwen/aiplus/blob/main/docs/roadmap/v0.5.2-known-gaps.md).

## License

[Apache-2.0](LICENSE)
