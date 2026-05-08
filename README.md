# AiPlus Auto Compact

[中文 README](README.zh-CN.md)

AiPlus Auto Compact is the compact/checkpoint/resume workflow module used by
the AiPlus CLI (`aiplus`). It helps Codex, Claude Code, and OpenCode keep
project-local handoff state before and after a context compact.

It does not click a compact button, call `/compact`, upload data, change global runtime settings, or make compact safe automatically. It prepares local files, validates common structure, writes checkpoints, and reminds the agent what to read next.

## Beginner Flow

### 1. Install the module into your project

If `aiplus` is already on your `PATH`, run this from the project you want to
protect:

```bash
cd MyProject
aiplus install codex
```

For other runtimes:

```bash
aiplus install claude-code
aiplus install opencode
aiplus install all
```

The install is project-local. It may write `.aiplus/`, `.codex/compact/`, project `.claude/` files, project `.opencode/` files, and the AiPlus managed block in project `AGENTS.md`. It does not modify global Codex, Claude Code, OpenCode, shell, or package-manager configuration.

### 2. Type this in your already-open agent session

After installing in the same project, tell the active agent to refresh:

```text
刷新
```

English also works:

```text
refresh
```

Meaning: reread `AGENTS.md`, reread `.aiplus/AGENTS.aiplus.md`, read `.codex/compact/current-handoff.md` if present, enable AiPlus guidance, and continue the current task.

### 3. Use compact commands during long work

```bash
aiplus status
aiplus doctor
aiplus compact validate
aiplus compact checkpoint
aiplus compact resume
aiplus uninstall --dry-run
```

Before compact, run:

```bash
aiplus compact validate
aiplus compact checkpoint
```

Only recommend manual compact after `checkpoint` returns `SAFE_TO_COMPACT` and every Owner gate is explicitly `APPROVED`. `UNKNOWN_PENDING` means `UNKNOWN_NEEDS_REVIEW`; `DENIED` blocks compact recommendation.

After the user or host runtime completes compact, run:

```bash
aiplus compact resume
```

## Runtime Choices

| Runtime | Install command | Auto compact support | Recommended use |
| --- | --- | --- | --- |
| Codex | `aiplus install codex` | Reminder/checkpoint only | Run `aiplus compact checkpoint`, review output, then manually use Codex `/compact` or UI compact. |
| Claude Code | `aiplus install claude-code` | Optional reviewed hooks and commands | Use project-local Claude Code adapter files; review hooks before enabling them. |
| OpenCode | `aiplus install opencode` | Project-local command workflow | Use project `.opencode/` commands, agents, and prompts; keep global config unchanged. |

Compatibility aliases are supported by the AiPlus CLI:

```bash
aiplus install claude
aiplus install cc
aiplus install oc
aiplus install --runtime codex
aiplus install --all-runtimes
```

## What This Repository Contains

This repository is the compact workflow module source, not the full AiPlus CLI
distribution.

- `core/templates/`: compact handoff templates.
- `core/schemas/`: JSON schemas for policy and ledger structure.
- `core/docs/`: protocol, Owner gate, and checkpoint/resume reference.
- `adapters/codex/`: Codex plugin and skill assets.
- `adapters/claude-code/`: Claude Code plugin-shaped commands, skill, and optional hook example.
- `adapters/opencode/`: OpenCode project-local config, commands, agents, and prompts.
- `examples/`: synthetic examples for all three runtimes.
- `core/scripts/compactctl.mjs`: legacy standalone Node helper retained for compatibility tests and migration reference.

## What It Can Automate

AiPlus Auto Compact can:

- Create local compact state files in a target project.
- Preserve existing compact files unless an explicit force flow is used.
- Validate required files, sections, enum values, policy JSON, version fields, Owner gates, next actions, and obvious sensitive patterns.
- Write local checkpoint JSON under `.codex/compact/checkpoints/`.
- Print resume-oriented state after compaction.
- Keep runtime setup project-local by default.

## What It Cannot Automate

AiPlus Auto Compact cannot:

- Click UI controls, call `/compact`, or force any runtime to compact.
- Verify that compaction is operationally appropriate for every project.
- Detect every secret, private path, or personal-data pattern.
- Replace human review of Owner gates.
- Publish packages, create tags, upload artifacts, or configure cloud services.
- Make Codex, Claude Code, and OpenCode share one internal context implementation.

## Safety And Privacy Boundaries

Keep compact state local unless it has been intentionally reviewed and redacted. Do not place secrets, tokens, API keys, private keys, raw transcripts, provider request or response bodies, HAR/WebRTC dumps, private account identifiers, private paths, or personal data in compact files.

Use placeholders in public docs and examples:

- `<REPO_ROOT>`
- `<TARGET_PROJECT>`
- `<OWNER>`
- `<GITHUB_OWNER>`
- `<EXAMPLE_PROJECT>`
- `<REDACTED_SECRET>`
- `<REDACTED_TOKEN>`
- `<REDACTED_PII>`

Validation is structural and heuristic. A validation pass means the files satisfy the local contract; it does not prove the project is ready to compact, safe to publish, compliant, or private.

## Advanced: Legacy Node Reference

The current ordinary-user path is the AiPlus CLI (`aiplus`). The older
standalone Node helper remains in this repository for compatibility and
migration review:

```bash
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs init
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs validate
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs checkpoint
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs resume
```

Use this only when you are auditing the legacy workflow or testing this module
without the AiPlus CLI.

## Validation

Run package acceptance tests from this repository:

```bash
cd <REPO_ROOT>/aiplus-auto-compact
npm test
node --check core/scripts/compactctl.mjs
```

Run compact checks from a target project where `aiplus` is available:

```bash
cd MyProject
aiplus compact validate
aiplus compact checkpoint
aiplus compact resume
```

Exit codes used by the legacy helper and mirrored by the AiPlus compact flow:

- `0`: pass or safe.
- `1`: blocked or invalid.
- `2`: unknown or inconclusive.
- `3`: internal error.

## Examples

Synthetic examples are provided for:

- [Codex project](examples/codex-project/README.md)
- [Claude Code project](examples/claude-code-project/README.md)
- [OpenCode project](examples/opencode-project/README.md)

These examples use placeholders only and are not copied from private projects.

## Migration

`codex-compact-protocol` is the legacy Codex-first public record. AiPlus Auto
Compact is the current cross-agent compact workflow module, and the AiPlus CLI
(`aiplus`) is the current user-facing path.

Existing `.codex/compact/` state remains compatible. For details, read [Migration From Codex Compact Protocol](docs/migration-from-codex-compact-protocol.md).

## Current Release Status

This repository is public on GitHub as `aiplus-auto-compact`. It is not
published to npm, Cargo, Homebrew, any package registry, or any marketplace. The
AiPlus CLI (`aiplus`) is the intended distribution surface for ordinary users.
Future tags, GitHub Releases, package publications, binary uploads, marketplace
submissions, or installer publication require separate Owner approval.

## Contributing

Keep contributions local-first and runtime-neutral unless the change is explicitly adapter-specific. Public docs and examples must use placeholders instead of private paths or real project data.

Before proposing a release change:

```bash
cd <REPO_ROOT>/aiplus-auto-compact
npm test
node --check core/scripts/compactctl.mjs
```

Document behavior changes in `CHANGELOG.md`, update `MODULES.md` when ownership or file layout changes, and update `RELEASE_CHECKLIST.md` before publication review.
