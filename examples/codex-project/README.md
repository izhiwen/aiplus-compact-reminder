# Synthetic Codex Project Example

This example shows how a Codex user can apply AiPlus Auto Compact to a synthetic project named `<EXAMPLE_PROJECT>`.

## Layout

```text
<TARGET_PROJECT>/
  src/
  tests/
  .codex/compact/
```

## Commands

```bash
cd <TARGET_PROJECT>
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs init
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs validate
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs checkpoint
```

If checkpoint output is acceptable after review, the user manually runs Codex compact through the Codex UI or `/compact`.

After compact:

```bash
cd <TARGET_PROJECT>
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs resume
```

## Synthetic Handoff Content

Use placeholder-only entries:

```text
Current goal: Finish synthetic feature validation for <EXAMPLE_PROJECT>.
Current phase: Verify docs and tests.
Owner gate: APPROVED: <OWNER> approved manual compact review for synthetic example state.
Next safe action: Run validation in <TARGET_PROJECT> and inspect checkpoint output.
Evidence: npm test passed in <REPO_ROOT>/aiplus-auto-compact.
```

Do not include real private paths, account data, transcripts, screenshots, provider payloads, or secrets.
