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
aiplus install codex        # or: claude-code, opencode, all
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

## Runtime Support

| Runtime | Install command | Compact support |
|---------|----------------|----------------|
| Codex | `aiplus install codex` | Reminder and checkpoint only |
| Claude Code | `aiplus install claude-code` | Reviewed hooks and commands |
| OpenCode | `aiplus install opencode` | Project-local command workflow |
| All | `aiplus install all` | All three runtimes |

## What's Inside

- `core/templates/` — Compact handoff templates with role-aware sections
- `core/schemas/` — JSON schemas for context-capsule and reminder-state
- `core/docs/protocol.md` — Compact protocol reference
- `adapters/codex/` — Codex plugin assets
- `adapters/claude-code/` — Claude Code commands and hooks
- `adapters/opencode/` — OpenCode commands and prompts
- `examples/` — Synthetic examples for all three runtimes

## Common Commands

```bash
aiplus compact remind       # Check if compact is recommended
aiplus compact prepare      # Build context capsule and handoff
aiplus compact checkpoint   # Validate readiness
aiplus compact resume       # Resume after compact
aiplus compact savings      # Show token and cost savings
```

## Safety Boundaries

AiPlus Auto Compact does not:
- Click UI controls or call `/compact` for you
- Wake a host runtime waiting for user input
- Detect every possible secret or private pattern
- Replace human review of Owner gates
- Upload prompts, checkpoints, or savings data

## Roadmap

See the [main AiPlus repo](https://github.com/izhiwen/aiplus/blob/main/docs/roadmap/v0.5.2-known-gaps.md) for current gaps.

## License

[Apache-2.0](LICENSE)
