# Checkpoint And Resume Flow

The core flow is manual and local-first. It prepares evidence for a compact/resume boundary; it does not press, call, or automate any host agent compact control.

## Initialize

Run from a target repository:

```bash
node <PROJECT_ROOT>/core/scripts/compactctl.mjs init
```

`init` creates `.codex/compact/` and required state files from `core/templates/`. Existing state is preserved unless `--force` is supplied.

## Validate

```bash
node <PROJECT_ROOT>/core/scripts/compactctl.mjs validate
```

Exit codes:

- `0`: structural validation passed.
- `1`: validation failed or sensitive material was detected.
- `2`: state needs owner or adapter review before it can be trusted.

## Checkpoint

```bash
node <PROJECT_ROOT>/core/scripts/compactctl.mjs checkpoint
```

`checkpoint` writes a redacted JSON checkpoint under `.codex/compact/checkpoints/`. The checkpoint includes validation status, owner-gate state, review items, warnings, errors, dirty git summary when available, and the next safe action.

## Resume

```bash
node <PROJECT_ROOT>/core/scripts/compactctl.mjs resume
```

`resume` prints a compact single-line summary of the current goal, phase, blockers, owner gates, and next safe action. A host agent can use this output to recover context after compacting, but should still read the local state files in the recovery order listed in `current-handoff.md`.
