# Synthetic Claude Code Project Example

This example shows how a Claude Code user can apply AiPlus Auto Compact to a synthetic project named `<EXAMPLE_PROJECT>`.

## Load Adapter

```bash
claude --plugin-dir <REPO_ROOT>/aiplus-auto-compact/adapters/claude-code
```

Then use the adapter command documents in Claude Code:

```text
/aiplus-auto-compact:compact-validate
/aiplus-auto-compact:compact-checkpoint
/aiplus-auto-compact:compact-resume
```

## Shared Core Commands

The shared core CLI remains available from the target project:

```bash
cd <TARGET_PROJECT>
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs init
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs validate
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs checkpoint
```

After the user manually completes the Claude Code compact or session reset step:

```bash
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs resume
```

## Synthetic Handoff Content

Use placeholder-only entries:

```text
Current goal: Prepare Claude Code handoff for <EXAMPLE_PROJECT>.
Current phase: Review generated state files.
Owner gate: UNKNOWN_PENDING: <OWNER> has not reviewed the synthetic checkpoint yet.
Next safe action: Ask <OWNER> to review the checkpoint before compacting.
Evidence: Adapter command documents were loaded from <REPO_ROOT>.
```

Optional hooks in the Claude Code adapter are examples for reviewed local use. They should be inspected and adapted before enabling.
