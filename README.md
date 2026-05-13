# AiPlus Compact Reminder
[![CI](https://github.com/izhiwen/AiPlus-Compact-Reminder/actions/workflows/ci.yml/badge.svg)](https://github.com/izhiwen/AiPlus-Compact-Reminder/actions/workflows/ci.yml)
[![License: Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)

[中文 README](README.zh-CN.md)

## The pain

Long Codex / Claude Code / OpenCode sessions **burn tokens**. The same
context gets reloaded again and again as the window fills, and by the time
you compact, you have already paid the bill. Three specific failure modes
combine to make this expensive:

1. **The window is overflowing before you notice.** You are deep in a
   feature, the agent is producing code, and nobody is watching the token
   meter. By the time anyone notices, you have been re-paying for the same
   context tokens on every turn.
2. **You don't know when is a *good* time to compact.** Mid-task, and you
   lose the half-finished state. End-of-task, and you missed the chance to
   keep going on a fresh window. There is no obvious safe handoff point —
   so you keep burning tokens to defer the decision.
3. **A direct compact breaks task handoff and continuity.** Without
   preparation, the handoff is gone. The decision log is truncated. After
   resume, the agent is amnesiac and asks questions it already had answers
   to. You re-explain the task, re-establish constraints, rebuild context
   from scratch — burning *more* tokens to rebuild what you just lost.

The net effect: a long session costs 3-5× what it should. The token meter
is the single largest controllable cost in agent-driven development.

## What we do about it

AiPlus Compact Reminder is built around one goal: **save tokens**. It does
that by turning compact from a panic operation into a planned one with
clean handoff and clean resume.

**It reminds you when it is appropriate to compact** — early enough to
save tokens, late enough to keep useful context. The signal combines a
token threshold with task-handoff-point detection, so the recommendation
lands at a natural seam in the work, not mid-sentence.

**It tells you how much you saved.** `aiplus compact savings` shows the
token and dollar count avoided by compacting at the right moment versus
letting the session run on.

**It auto-prepares a structured handoff before compaction:**

- `current-handoff` — what you were doing, what comes next
- `decision-log` — decisions made and why (so resume picks up the *reasoning*,
  not just the code)
- `agent-state-ledger` — current task state, open questions, planned actions
- `evidence-ledger` — supporting context and references

**It auto-resumes after compaction.** The capsule is checksum-verified and
extracted automatically. The decision ledger is restored, so the agent
continues from the exact point it left off — with full memory of prior
choices, not from zero.

If the capsule is missing or malformed, it falls back to the legacy handoff
format gracefully. You don't get stranded.

## Quick start

If you already have AiPlus installed:

```bash
aiplus install
cd MyProject
aiplus compact init
```

Then the subcommands you would expect:

```bash
aiplus compact remind        # check if compact is recommended right now
aiplus compact prepare       # build the handoff + capsule
aiplus compact checkpoint    # validate readiness before you compact
aiplus compact resume        # restore from capsule after compact
aiplus compact savings       # how many tokens and dollars this saved
```

Or install as a standalone module on an existing AiPlus project:

```bash
aiplus add compact-reminder
aiplus install codex          # or: claude-code, opencode, all
```

The CLI subcommand `aiplus compact` is unchanged — your muscle memory still
works.

## What's inside

- `core/templates/` — handoff templates (current-handoff, decision-log,
  agent-state-ledger, evidence-ledger)
- `core/schemas/` — JSON schemas for context-capsule and reminder state
- `core/docs/protocol.md` — full compact protocol reference
- `adapters/codex/` — Codex adapter and `compact` commands
- `adapters/claude-code/` — Claude Code adapter and commands
- `adapters/opencode/` — OpenCode adapter and commands

## Safety boundaries

Compact Reminder is a preparation tool, not an autopilot. It does not:

- click UI controls or call `/compact` for you (you still trigger compact)
- wake a host runtime that is waiting for user input
- detect every possible secret or private pattern (structural checks only)
- replace human review of Owner gates
- upload prompts, checkpoints, or savings data

## Contributing

We welcome contributions that stay within the plugin's scope (token-saving
compact + structured resume, not a general session manager).

1. **Open an issue first** for anything larger than a typo fix.
2. **Run `aiplus compact remind` and `aiplus compact savings`** before and
   after your change to confirm the token-saving signal still fires.
3. **Adapter parity** — if you change CLI surface, update all three
   adapters (`adapters/codex/`, `adapters/claude-code/`, `adapters/opencode/`).

## More

- Main platform: [AiPlus](https://github.com/izhiwen/AiPlus)
- Tracked work before next release:
  [v0.5.2 known gaps](https://github.com/izhiwen/AiPlus/blob/main/docs/roadmap/v0.5.2-known-gaps.md)

## License

[Apache-2.0](LICENSE)
