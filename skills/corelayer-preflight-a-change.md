---
name: Preflight a change against org memory before a PR
description: Before opening a pull request, check the change against Corelayer organization memory and surface failure modes the org has hit before.
api: https://docs.corelayer.com/cli/overview
operations: [corelayer.preflight, corelayer.search_org_memory]
method: generated
source: https://docs.corelayer.com/cli/overview + https://docs.corelayer.com/memory/overview
---

# Preflight a change against org memory before a PR

Corelayer's organization memory learns failure patterns from past incidents. Preflight is advisory — it reads memory and never blocks a push.

## Authenticate

```bash
export CORELAYER_API_KEY=cl_live_...
export CORELAYER_API_URL=https://api.corelayer.com
```

## Steps

1. **Run preflight from inside the repo.** With no arguments it derives PR context from the current branch (repo from the git remote; title/description from commits and changed files since the base branch):
   - CLI: `corelayer preflight`
   - Include the diff: `corelayer preflight --diff`
   - Machine-readable: `corelayer preflight --json` (returns `checks`, `consideredCount`, `pulledCount`)
   - MCP: `corelayer.preflight` (pass the PR description)
2. **Read each check.** Every check names the memory behind it, its severity, and how often the org has seen it. An empty result means memory had nothing relevant to flag.
3. **Optionally search memory for deeper context.**
   - MCP: `corelayer.search_org_memory` — search organization memory for prior decisions about a failing area.

## Rules

- Present results as reminders to double-check, not blockers.
- If preflight returns nothing, say memory had nothing to flag.
- Preflight is read-only and advisory; it never blocks a push.
