# Compact Reminder

[简体中文](README.zh-CN.md)

## The Problem

Your session stalls because you forget to run `compact` until the context window
is already overflowing. By then, the agent has already started forgetting early
requirements, and you are forced to compact in a panic.

It is unclear when is a good time to compact. Mid-task compaction means state
loss; end-of-task compaction means wasted opportunity. You have no signal for
the safe handoff point.

Direct compaction without preparation breaks task handoff and continuity. Your
handoff is lost, the decision log is truncated, and the agent feels amnesiac
after resume. You must re-explain the task, re-establish constraints, and
reconstruct everything from memory.

## The Solution

Compact Reminder actively reminds you when compaction is appropriate. It
combines a token threshold with task-handoff-point detection so you compact at
the right moment, not too early and not too late.

Before compaction, it auto-prepares a structured handoff:

- **current-handoff** — What you were doing and what comes next
- **decision-log** — Decisions made and why
- **agent-state-ledger** — Current task state, open questions, next actions
- **evidence-ledger** — Supporting context and references

After compaction, it auto-resumes via the capsule. The decision ledger is
extracted and restored, so the agent continues from the exact point it left off
with full awareness of prior decisions and state. If the capsule is missing or
malformed, it falls back to the legacy handoff format gracefully.

## Quick Start

### Bundled (recommended)

If you already use AiPlus:

```bash
aiplus install
cd MyProject
aiplus compact init
```

Then use the subcommands you already know:

```bash
aiplus compact remind       # Check if compaction is recommended
aiplus compact prepare      # Build context capsule and handoff
aiplus compact checkpoint   # Validate readiness before compact
aiplus compact resume       # Restore context from capsule after compact
aiplus compact savings      # Show token and cost savings
```

### Standalone

```bash
git clone https://github.com/izhiwen/aiplus-compact-reminder.git
cd aiplus-compact-reminder
```

The CLI subcommand `aiplus compact` is unchanged for muscle memory.

## What's Inside

- `core/templates/` — Structured handoff templates (current-handoff,
  decision-log, agent-state-ledger, evidence-ledger)
- `core/schemas/` — JSON schemas for context-capsule and state validation
- `core/docs/protocol.md` — Complete compact protocol reference
- `adapters/codex/` — Codex adapter and compact commands
- `adapters/claude-code/` — Claude Code adapter and commands
- `adapters/opencode/` — OpenCode adapter and commands
- `core/scripts/compactctl.mjs` — Legacy Node helper (archived, compatibility
  tests only)

## Safety Boundaries

Compact Reminder does not:

- Click UI controls or call `/compact` for you
- Wake a host runtime that is waiting for user input
- Detect every possible secret or private pattern (structural checks only)
- Replace human review of Owner gates
- Upload prompts, checkpoints, or savings data

## More Info

See the [main AiPlus repository](https://github.com/izhiwen/aiplus) for the
complete platform.

Current gaps and planned work:
[v0.5.2 known gaps](https://github.com/izhiwen/aiplus/blob/main/docs/roadmap/v0.5.2-known-gaps.md)

## License

[Apache-2.0](LICENSE)
