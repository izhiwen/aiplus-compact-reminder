# Synthetic OpenCode Project Example

This example shows how an OpenCode user can apply AiPlus Auto Compact to a synthetic project named `<EXAMPLE_PROJECT>`.

Use the project-local OpenCode adapter when command, agent, prompt, and `opencode.json` examples are useful. The shared core CLI remains the source of structural validation.

## Commands

```bash
cd <TARGET_PROJECT>
mkdir -p .opencode/agents .opencode/commands .opencode/prompts
cp <REPO_ROOT>/aiplus-auto-compact/adapters/opencode/opencode.json.example .opencode/opencode.json
cp <REPO_ROOT>/aiplus-auto-compact/adapters/opencode/agents/*.md .opencode/agents/
cp <REPO_ROOT>/aiplus-auto-compact/adapters/opencode/commands/*.md .opencode/commands/
cp <REPO_ROOT>/aiplus-auto-compact/adapters/opencode/prompts/*.md .opencode/prompts/
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs init
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs validate
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs checkpoint
```

Use OpenCode's own compact or session-management controls manually after reviewing the checkpoint output.

After compact:

```bash
cd <TARGET_PROJECT>
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs resume
```

## Synthetic Handoff Content

Use placeholder-only entries:

```text
Current goal: Preserve OpenCode context for <EXAMPLE_PROJECT>.
Current phase: Complete checkpoint review.
Owner gate: DENIED: <OWNER> wants one more validation pass before compacting.
Next safe action: Fix validation findings in <TARGET_PROJECT> and rerun checkpoint.
Evidence: Synthetic OpenCode usage documented with placeholders only.
```

Record any OpenCode-specific compact steps in `current-handoff.md` using placeholders rather than real local paths or private project details.
