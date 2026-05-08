# Codex Adapter

This adapter preserves the proven Codex compact/resume workflow from `codex-compact-protocol` while aligning ordinary users with the Rust `aiplus` CLI.

## What It Does

- Provides a Codex plugin manifest at `.codex-plugin/plugin.json`.
- Provides a Codex skill at `skills/compact-protocol/SKILL.md`.
- Provides project-local guidance that can be installed by `aiplus install codex`.
- Keeps legacy Node helper references available for migration review.

## What It Cannot Do

This adapter cannot force Codex UI compact, click UI controls, or call `/compact` for the user. It prepares checkpoint/resume state only. The user still manually runs `/compact` or uses the Codex UI compact control after review.

## Local Use

From a target project:

```bash
aiplus install codex
aiplus compact validate
aiplus compact checkpoint
```

Only recommend manual compact after `checkpoint` returns `SAFE_TO_COMPACT` and every Owner gate is explicitly `APPROVED`. `UNKNOWN_PENDING` remains `UNKNOWN_NEEDS_REVIEW`; `DENIED` blocks compact recommendation.

After compact:

```bash
aiplus compact resume
```

## Shared Core

The Codex adapter intentionally does not duplicate the core implementation. Shared assets live in:

```text
../../core/schemas/
../../core/templates/
../../core/docs/
```

This keeps Codex behavior aligned with Claude Code and OpenCode adapters.

`../../core/scripts/compactctl.mjs` remains available as a legacy standalone
helper for audits and compatibility tests, but it is not the ordinary-user
Rust `aiplus` path.
