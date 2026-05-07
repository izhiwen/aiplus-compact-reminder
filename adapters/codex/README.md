# Codex Adapter

This adapter preserves the proven Codex compact/resume workflow from `codex-compact-protocol` while using the shared AiPlus Auto Compact core for scripts, schemas, templates, fixtures, and tests.

## What It Does

- Provides a Codex plugin manifest at `.codex-plugin/plugin.json`.
- Provides a Codex skill at `skills/compact-protocol/SKILL.md`.
- Points users to the shared core CLI at `../../core/scripts/compactctl.mjs` from this adapter directory, or `core/scripts/compactctl.mjs` from the repository root.

## What It Cannot Do

This adapter cannot force Codex UI compact, click UI controls, or call `/compact` for the user. It prepares checkpoint/resume state only. The user still manually runs `/compact` or uses the Codex UI compact control after review.

## Local Use

From a target project:

```bash
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs init
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs validate
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs checkpoint
```

Only recommend manual compact after `checkpoint` returns `SAFE_TO_COMPACT` and every Owner gate is explicitly `APPROVED`. `UNKNOWN_PENDING` remains `UNKNOWN_NEEDS_REVIEW`; `DENIED` blocks compact recommendation.

After compact:

```bash
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs resume
```

## Shared Core

The Codex adapter intentionally does not duplicate the core implementation. Shared assets live in:

```text
../../core/scripts/compactctl.mjs
../../core/schemas/
../../core/templates/
../../core/docs/
```

This keeps Codex behavior aligned with Claude Code and OpenCode adapters.
