# Migration From Codex Compact Protocol

This guide is for users moving from `codex-compact-protocol` to AiPlus Auto Compact.

## What Changed

AiPlus Auto Compact keeps the compact/resume state model and CLI workflow, but separates the project into:

- A shared runtime-neutral core under `<REPO_ROOT>/aiplus-auto-compact/core/`.
- Runtime adapters under `<REPO_ROOT>/aiplus-auto-compact/adapters/`.
- Root public docs and synthetic examples under `<REPO_ROOT>/aiplus-auto-compact/`.

The core protocol still uses the compatibility state path:

```text
<TARGET_PROJECT>/.codex/compact/
```

This keeps existing handoff files and fixtures easier to review during migration.

## What Did Not Change

The manual compact boundary remains the same:

1. Update local handoff files.
2. Run validation.
3. Create a checkpoint.
4. Review Owner gates and checkpoint output.
5. Manually trigger the runtime's compact action when appropriate.
6. Resume from local files.

The tool still cannot trigger Codex UI compact, call `/compact`, or control Claude Code or OpenCode compaction.

## Path Mapping

| Previous concept | AiPlus Auto Compact location |
| --- | --- |
| Protocol templates | `<REPO_ROOT>/aiplus-auto-compact/core/templates/` |
| Protocol schemas | `<REPO_ROOT>/aiplus-auto-compact/core/schemas/` |
| Helper CLI | `<REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs` |
| Target project state | `<TARGET_PROJECT>/.codex/compact/` |
| Codex-specific docs and assets | `<REPO_ROOT>/aiplus-auto-compact/adapters/codex/` |
| Claude Code command assets | `<REPO_ROOT>/aiplus-auto-compact/adapters/claude-code/` |

## Migration Steps

From the target project:

```bash
cd <TARGET_PROJECT>
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs validate
```

If validation passes, create a new checkpoint with the shared core CLI:

```bash
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs checkpoint
```

If validation reports unsupported or missing versions, review the files under `.codex/compact/` before editing. Unknown versions should be treated as review items, not automatic upgrades.

For a fresh target project:

```bash
cd <TARGET_PROJECT>
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs init
```

Use `--force` only when `<OWNER>` intends to replace existing compact files with templates.

## Codex Users

Use the shared core CLI as the default v0.2 workflow:

```bash
cd <TARGET_PROJECT>
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs validate
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs checkpoint
```

Keep Codex compact manual. After compact:

```bash
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs resume
```

## Claude Code Users

Load the Claude Code adapter when command documents are useful:

```bash
claude --plugin-dir <REPO_ROOT>/aiplus-auto-compact/adapters/claude-code
```

Then use the namespaced commands to guide validation, checkpoint, and resume. The shared core CLI remains the source of structural validation.

## OpenCode Users

Use the OpenCode adapter when project-local config, commands, agents, and prompts are useful:

```bash
cp <REPO_ROOT>/aiplus-auto-compact/adapters/opencode/opencode.json.example <TARGET_PROJECT>/.opencode/opencode.json
```

Merge rather than overwrite if the target project already has `.opencode/opencode.json`. The shared core CLI remains the source of structural validation, and OpenCode compact/session controls remain manual.

## Review Checklist

- Existing `.codex/compact/` files are still relevant.
- Owner gates are explicit as `APPROVED`, `DENIED`, or `UNKNOWN_PENDING`.
- No compact file contains secrets, tokens, private paths, raw transcripts, provider payloads, account identifiers, or personal data.
- `compact-policy.json` has known `templateVersion` and `schemaVersion` values.
- `validate` has been rerun after edits.
- `checkpoint` output has been reviewed before manual compaction.
