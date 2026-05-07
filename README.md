# AiPlus Auto Compact

AiPlus Auto Compact is a local compact/resume protocol for long agent sessions. It prepares structured handoff files, validates them, writes checkpoints, and gives the next runtime enough local state to resume after a context compaction.

It does not trigger compaction for you. Codex, Claude Code, and OpenCode still require the user or host runtime to perform the actual compact action. AiPlus Auto Compact helps decide whether the local handoff state is ready to review before that action.

## Five Minute Start

Requirements:

- Node.js 18 or newer.
- This repository at `<REPO_ROOT>`.
- A target project at `<TARGET_PROJECT>`.

Initialize compact state in a target project:

```bash
cd <TARGET_PROJECT>
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs init
```

Edit the generated files under:

```text
<TARGET_PROJECT>/.codex/compact/
```

At minimum, fill in the current goal, current phase, blockers, Owner gates, next actions, decisions, agent state, and evidence.

Validate the state:

```bash
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs validate
```

Create a checkpoint before compact:

```bash
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs checkpoint
```

Resume after compact:

```bash
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs resume
```

## Runtime Quick Starts

### Codex

Use the shared core CLI from the project you want to protect:

```bash
cd <TARGET_PROJECT>
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs init
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs validate
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs checkpoint
```

Review the checkpoint result. If the state is acceptable, manually run Codex compact using the Codex UI or `/compact`. After compact, run:

```bash
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs resume
```

### Claude Code

Load the Claude Code adapter when you want namespaced prompt commands:

```bash
claude --plugin-dir <REPO_ROOT>/aiplus-auto-compact/adapters/claude-code
```

Available adapter command documents:

- `/aiplus-auto-compact:compact-validate`
- `/aiplus-auto-compact:compact-checkpoint`
- `/aiplus-auto-compact:compact-resume`

The command documents guide Claude Code through the same local protocol. They do not run shell commands automatically unless the agent and user explicitly choose that step.

### OpenCode

Use the project-local OpenCode adapter when you want example commands, agents, prompts, and `opencode.json` configuration:

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

Then use OpenCode's own compact or session-management controls manually. After compaction, run `resume` and have the next agent read the local state files in the order listed in `current-handoff.md`.

## Runtime Comparison

| Runtime | Adapter | Auto compact support | Recommended v0.2 usage |
| --- | --- | --- | --- |
| Codex | Shared core CLI plus existing Codex adapter assets | No. User manually triggers Codex compact after review. | Use `compactctl.mjs` for `init`, `validate`, `checkpoint`, and `resume`; keep compact action manual. |
| Claude Code | Claude Code plugin-shaped adapter with command documents and optional hook example | No. Commands and hooks can remind or guide, but do not perform full compaction control. | Load the adapter locally, use namespaced commands for review, and run shared core validation before compacting. |
| OpenCode | Project-local config, commands, agents, and prompts | No. Use OpenCode's own controls manually. | Use project-local adapter files plus the shared core CLI; do not modify global config by default. |

## Shared Core And Adapters

The shared core is the runtime-neutral part:

- Templates for `.codex/compact/` state.
- JSON schemas for policy and parsed handoff data.
- `compactctl.mjs` commands for `init`, `validate`, `checkpoint`, and `resume`.
- Core docs for protocol, Owner gates, and checkpoint/resume flow.

Adapters are runtime-facing wrappers or prompts:

- Codex adapter assets preserve the existing Codex compact protocol workflow.
- Claude Code adapter assets provide plugin-shaped command documents and an optional hook example.
- OpenCode adapter assets provide project-local config, commands, agents, and prompts.

Adapters should not fork the protocol unless they also document compatibility and migration behavior.

## What It Can Automate

AiPlus Auto Compact can:

- Create local compact state files in a target project.
- Preserve existing compact files unless `--force` is used.
- Validate required files, sections, enum values, policy JSON, version fields, Owner gates, next actions, and obvious sensitive patterns.
- Write local checkpoint JSON under `.codex/compact/checkpoints/`.
- Print resume-oriented state after compaction.
- Normalize checkpoint working-directory output as `<REPO_ROOT>`.

## What It Cannot Automate

AiPlus Auto Compact cannot:

- Click UI controls, call `/compact`, or force any runtime to compact.
- Verify that compaction is operationally appropriate for every project.
- Detect every secret, private path, or personal-data pattern.
- Replace human review of Owner gates.
- Publish packages, push commits, create tags, upload artifacts, or configure cloud services.
- Make Codex, Claude Code, or OpenCode share the same internal context implementation.

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

Validation is structural and heuristic. A validation pass means the files satisfy the local contract; it does not prove the project is ready to compact.

## Validation

Run the package acceptance test:

```bash
cd <REPO_ROOT>/aiplus-auto-compact
npm test
```

Run protocol validation in a target project:

```bash
cd <TARGET_PROJECT>
node <REPO_ROOT>/aiplus-auto-compact/core/scripts/compactctl.mjs validate
```

Exit codes:

- `0`: structural validation passed.
- `1`: validation failed or blocking state was found.
- `2`: state requires review before it can be trusted.
- `3`: internal error.

## Examples

Synthetic examples are provided for:

- [Codex project](examples/codex-project/README.md)
- [Claude Code project](examples/claude-code-project/README.md)
- [OpenCode project](examples/opencode-project/README.md)

These examples use placeholders only and are not copied from private projects.

## Migration

If you used `codex-compact-protocol`, read [Migration From Codex Compact Protocol](docs/migration-from-codex-compact-protocol.md).

## Current Release Status

This workspace package is prepared for GitHub publication review and is not published to any package registry. `package.json` currently marks it private and versioned as `0.1.0`. Any release, tag, package publish, or adapter distribution requires separate Owner approval.

## Contributing

Keep contributions local-first and runtime-neutral unless the change is explicitly adapter-specific. Public docs and examples must use placeholders instead of private paths or real project data.

Before proposing a release change:

```bash
cd <REPO_ROOT>/aiplus-auto-compact
npm test
```

Document any behavior changes in `CHANGELOG.md`, update `MODULES.md` when ownership or file layout changes, and update `RELEASE_CHECKLIST.md` before publication review.
