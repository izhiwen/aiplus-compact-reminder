# Release Gate

Scope: local QA/release readiness review for `aiplus-auto-compact`.

## Gate Status

LOCAL PASS BEFORE GITHUB PUBLICATION.

Local validation passed. GitHub remote/latest-commit verification must be performed after `git init`, initial commit, and push because this directory starts as a non-git local project tree.

## Required Checks

| Gate | Status | Evidence |
| --- | --- | --- |
| `core/scripts/compactctl.mjs` syntax | PASS | `rtk node --check core/scripts/compactctl.mjs`, exit 0 |
| Test suite | PASS | `rtk npm test`, 15/15 acceptance tests passed |
| Manifest/config JSON parse | PASS | Parsed package metadata, Codex plugin manifest, Claude plugin manifest, Claude hook example, and OpenCode config example |
| Adapter required files | PASS | Required Codex, Claude Code, and OpenCode adapter files exist |
| README runtime coverage | PASS | Root README covers Codex, Claude Code, OpenCode, shared core, adapters, validation, safety, and current release status |
| README link/path sanity | PASS | Relative markdown links checked with no broken links |
| Public-safety scan | PASS | No blocker; expected policy/detector/test matches only |
| Private-data scan | PASS | No real private data found; expected policy/detector matches only |
| Old Codex-only branding in root/core | PASS WITH CAVEAT | `.codex/compact` compatibility paths remain intentionally documented in core; no release blocker |
| No npm publish/tag/release | PASS | No package publish, tag, GitHub Release, marketplace submission, or global install performed |
| GitHub remote URL/latest commit after push | PENDING | Complete only after final GitHub publication step |

## Final Rerun Commands

Run from the final `aiplus-auto-compact` checkout before publication:

```bash
rtk node --check core/scripts/compactctl.mjs
rtk npm test
rtk node - <<'NODE'
const fs=require('fs'), path=require('path');
const want=new Set(['package.json','plugin.json','opencode.json.example','hooks.example.json']);
function walk(d,out=[]){for(const e of fs.readdirSync(d,{withFileTypes:true})){if(e.name==='.git'||e.name==='node_modules')continue; const p=path.join(d,e.name); if(e.isDirectory())walk(p,out); else if(want.has(e.name))out.push(p)} return out}
const files=walk('.').sort();
for (const f of files){JSON.parse(fs.readFileSync(f,'utf8')); console.log('OK '+f)}
NODE
rtk node - <<'NODE'
const fs=require('fs');
const required={
  'core':['scripts/compactctl.mjs','schemas/compact-policy.schema.json','templates/compact-policy.json','docs/protocol.md'],
  'adapters/codex':['.codex-plugin/plugin.json','skills/compact-protocol/SKILL.md','README.md'],
  'adapters/claude-code':['.claude-plugin/plugin.json','skills/compact-protocol/SKILL.md','commands/compact-checkpoint.md','commands/compact-resume.md','commands/compact-validate.md','README.md'],
  'adapters/opencode':['opencode.json.example','agents/compact-advisor.md','agents/compact-reviewer.md','commands/compact-checkpoint.md','commands/compact-resume.md','commands/compact-validate.md','prompts/compact-protocol.md','README.md'],
  'examples':['codex-project/README.md','claude-code-project/README.md','opencode-project/README.md']
};
let fail=false;
for(const [base,files] of Object.entries(required)){
 for(const rel of files){const p=base+'/'+rel; const ok=fs.existsSync(p); console.log((ok?'OK ':'MISSING ')+p); if(!ok) fail=true;}
}
process.exit(fail?1:0);
NODE
```

After GitHub publication, verify:

```bash
rtk git remote -v
rtk git log -1 --oneline
```

## Forbidden Actions

Not performed:

- npm publish
- package registry publish
- GitHub Release creation
- git tag release
- marketplace submission
- global install
- `$CODEX_HOME` modification
- deletion or transfer of `codex-compact-protocol`

## Handoff Notes

Owner gate needed: no for GitHub repo creation/push under the current approved goal. Separate Owner approval is still required for npm/package registry publish, tags, GitHub Releases, marketplace submission, global installs, and deletion or transfer of the existing Codex-first repo.
