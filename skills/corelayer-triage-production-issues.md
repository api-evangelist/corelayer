---
name: Triage production issues with Corelayer
description: Discover the right Corelayer group, list open production issues, and read root-cause context to triage incidents — via the Corelayer CLI or MCP server.
api: https://docs.corelayer.com/cli/overview
operations: [corelayer.list_groups, corelayer.list_issues, corelayer.get_issue, corelayer.get_issue_summary, issues diff]
method: generated
source: https://docs.corelayer.com/cli/overview + https://docs.corelayer.com/cli/mcp-server
---

# Triage production issues with Corelayer

Corelayer is an AI SRE / on-call platform. Use this skill to triage production issues from the terminal (CLI) or an agent (MCP). All reads are safe; treat close/reopen/delete as write operations that need explicit approval.

## Authenticate

Set an API key in the environment (do not run interactive `corelayer login` from an agent):

```bash
export CORELAYER_API_KEY=cl_live_...
export CORELAYER_API_URL=https://api.corelayer.com
```

Credentials resolve in order: `CORELAYER_API_KEY` env var, then `~/.corelayer/config.json`.

## Steps

1. **Find the group.** A group scopes issues and integrations.
   - CLI: `corelayer groups list --json`
   - MCP: `corelayer.list_groups`
   - Optionally set a default so later calls can omit `--group`: `corelayer config set default-group <group-id>`
2. **List open issues.**
   - CLI: `corelayer issues list --json --status Open`
   - MCP: `corelayer.list_issues` (supports filters + pagination; default limit 20, max 100)
3. **Read root cause on the notable ones.** Issues are addressable by UUID or slug.
   - CLI: `corelayer issues get <issue-id-or-slug> --json`
   - MCP: `corelayer.get_issue` — returns title, status, severity, root cause, next steps, trace context, and event timeline.
4. **See what changed after a deploy.**
   - CLI: `corelayer issues diff --duration 2hours --limit 50 --json` (or `--since 2026-06-04T20:00:00Z`)
5. **Summarize health.**
   - CLI: `corelayer issues summary --group <group-id>`
   - MCP: `corelayer.get_issue_summary`

## Rules

- Prefer `--json` whenever an agent must reason over output.
- Pagination uses `--limit`/`--page`; default 20, maximum 100.
- Closing many stale issues: use `bulk-close` (or `corelayer.bulk_close_issues`, local MCP only) rather than looping single closes — repeated single mutations hit HTTP 429 rate limits.
- Write tools (`issues close`/`reopen`/`delete`, and local MCP `close_issue`/`reopen_issue`/`bulk_close_issues`) require explicit user approval; remote MCP is read-only.
