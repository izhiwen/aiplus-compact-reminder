# AiPlus Compact Reminder
[中文 README](README.zh-CN.md)

## The pain

If you have ever run a long Codex / Claude Code / OpenCode session, you have
probably hit all three of these:

1. **You forget to compact.** You are deep in a feature, the agent is
   producing code, and nobody is watching the token meter. By the time
   anyone notices, the context window is already overflowing and the agent
   has started forgetting early requirements.
2. **You don't know when is a *good* time to compact.** Mid-task, and you
   lose the half-finished state. End-of-task, and you missed the chance to
   keep going on a fresh window. There is no obvious safe handoff point.
3. **A direct compact breaks task handoff and continuity.** Without
   preparation, the handoff is gone. The decision log is truncated. After
   resume, the agent is amnesiac and asks questions it already had answers
   to. You re-explain the task, re-establish constraints, rebuild context
   from scratch.

## What we do about it

AiPlus Compact Reminder turns compact from a panic operation into a planned
one.

**It reminds you when it is appropriate to compact.** Not when the meter is
already full. The signal combines a token threshold with task-handoff-point
detection — so the recommendation lands at a natural seam in the work, not
mid-sentence.

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

Or as a standalone module:

```bash
git clone https://github.com/izhiwen/aiplus-compact-reminder.git
cd aiplus-compact-reminder
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

## More

- Main platform: [aiplus](https://github.com/izhiwen/aiplus)
- Tracked work before next release:
  [v0.5.2 known gaps](https://github.com/izhiwen/aiplus/blob/main/docs/roadmap/v0.5.2-known-gaps.md)

## License

[Apache-2.0](LICENSE)
