# CLI Environment Reference

**IMPORTANT:** Every command in this file runs on the user's Mac, NOT inside the Cowork sandbox. You MUST use **Desktop Commander MCP tools** (`start_process`, `interact_with_process`, `read_file`) to execute these commands. The Cowork `Bash` tool runs inside an isolated Linux VM that does not have these tools or the user's credentials. If you try to run `git`, `gh`, `supabase`, etc. via the Cowork Bash tool, they will fail.

This reference documents the CLI tools available on the user's Mac via Desktop Commander, their authentication status, and common commands for each verification layer.

## Environment Summary

| Tool | Version | Path | Auth |
|------|---------|------|------|
| Homebrew | 5.1.0 | `/opt/homebrew/bin/brew` | n/a |
| Git | 2.50.1 | `/usr/bin/git` | via `gh` credential helper |
| GitHub CLI | 2.88.0 | `/opt/homebrew/bin/gh` | `kronik187` (keyring, scopes: gist, read:org, repo, workflow) |
| Node.js | 25.8.1 | `/opt/homebrew/bin/node` | n/a |
| npm | 11.11.0 | `/opt/homebrew/bin/npm` | n/a |
| Supabase CLI | 2.75.0 | `/opt/homebrew/bin/supabase` | Requires `supabase login` |
| Deno | 2.7.5 | `/opt/homebrew/bin/deno` | n/a |
| jq | 1.8.1 | `/opt/homebrew/bin/jq` | n/a |
| pgcli | 4.4.0 | `/opt/homebrew/bin/pgcli` | Requires connection string |

## Project Context

- **Repo:** `github.com/ainewfeed1/modular-alpha`
- **Local clone:** `~/Desktop/modular-alpha`
- **Branch:** `main`
- **Edge Functions:** 230
- **Migrations:** 660
- **Stack:** Vite + React + TailwindCSS + Supabase (Edge Functions on Deno)
- **Sentry Org:** `tea-qb` (region: `us.sentry.io`)
- **GitHub Account:** `kronik187`

## Desktop Commander Usage

All CLI commands run through Desktop Commander's `start_process` or `interact_with_process` tools. Key patterns:

```
# One-shot command (returns output directly)
start_process({ command: "cd ~/Desktop/modular-alpha && git log --oneline -5", timeout_ms: 10000 })

# Interactive session (for multi-step work)
start_process({ command: "python3 -i", timeout_ms: 5000 })
interact_with_process({ pid: <pid>, input: "import json; ..." })

# Read file content directly (no process needed)
read_file({ path: "/Users/agent1/Desktop/modular-alpha/supabase/functions/<name>/index.ts" })
```

## Command Reference by Verification Layer

### Layer 1: GitHub Commit Verification

```bash
# Fetch latest state
cd ~/Desktop/modular-alpha && git fetch origin

# Recent commits (quick check)
git log --oneline origin/main -5

# Full diff of latest commit
git diff origin/main~1..origin/main --stat

# Diff for specific directory/file
git diff origin/main~1..origin/main -- supabase/functions/<name>/

# Show specific commit details
git show <commit-hash> --stat

# GitHub API: rich commit data with CI status
gh api repos/ainewfeed1/modular-alpha/commits?per_page=3 | jq '.[].commit | {message: .message, date: .author.date}'

# Files changed in latest commit
gh api repos/ainewfeed1/modular-alpha/commits/HEAD | jq '.files[].filename'

# Check open PRs
gh pr list --repo ainewfeed1/modular-alpha

# Check all remote branches
gh api repos/ainewfeed1/modular-alpha/branches | jq '.[].name'

# See if main is behind a feature branch
git log --oneline origin/main..origin/<feature-branch>
```

### Layer 2: Edge Functions Verification

```bash
# List deployed functions (requires supabase login)
supabase functions list

# View function logs
supabase functions logs <function-name> --limit 20

# Read function source locally (no auth needed, no cache issues)
cat ~/Desktop/modular-alpha/supabase/functions/<function-name>/index.ts

# Search for verification markers across all functions
grep -r "VERIFICATION_MARKER\|console.log" ~/Desktop/modular-alpha/supabase/functions/<name>/

# Type-check a function locally with Deno
cd ~/Desktop/modular-alpha && deno check supabase/functions/<function-name>/index.ts

# List all edge functions (local)
ls -d ~/Desktop/modular-alpha/supabase/functions/*/

# Count edge functions
ls -d ~/Desktop/modular-alpha/supabase/functions/*/ | wc -l

# Check function dependencies (shared modules)
cat ~/Desktop/modular-alpha/supabase/functions/_shared/*.ts
```

### Layer 3: SQL / Migration Verification

```bash
# Latest migration files
ls -lt ~/Desktop/modular-alpha/supabase/migrations/ | head -5

# Read most recent migration
cat ~/Desktop/modular-alpha/supabase/migrations/$(ls ~/Desktop/modular-alpha/supabase/migrations/ | sort | tail -1)

# Search all migrations for a table or column
grep -rl '<table_name>' ~/Desktop/modular-alpha/supabase/migrations/

# Find CREATE TABLE statements
grep -l 'CREATE TABLE' ~/Desktop/modular-alpha/supabase/migrations/ | sort | tail -5

# Check for upsert patterns (ON CONFLICT)
grep -r 'ON CONFLICT' ~/Desktop/modular-alpha/supabase/migrations/

# Check for RLS policies in migrations
grep -r 'CREATE POLICY\|ALTER TABLE.*ENABLE ROW' ~/Desktop/modular-alpha/supabase/migrations/ | tail -10

# Migration list (requires supabase login)
supabase migration list

# Generate a diff of pending changes (requires supabase login)
supabase db diff

# Direct database connection (requires connection string)
pgcli <connection_string>
```

### Layer 4: Frontend Verification (CLI supplements)

```bash
# Build check — catches TypeScript/import errors before browser
cd ~/Desktop/modular-alpha && npm run build 2>&1 | tail -20

# Lint check
cd ~/Desktop/modular-alpha && npm run lint 2>&1 | tail -20

# Search for error patterns in source
grep -r "console.error\|throw new Error" ~/Desktop/modular-alpha/src/ --include="*.ts" --include="*.tsx" | grep -v node_modules | tail -10

# Check for missing imports
grep -r "from '.*'" ~/Desktop/modular-alpha/src/ --include="*.ts" | grep -v node_modules | grep "undefined\|Cannot find"

# Package dependency audit
cd ~/Desktop/modular-alpha && npm audit --audit-level=high 2>&1 | tail -10
```

### Hallucination Detection Commands

```bash
# Quick phantom implementation check
cd ~/Desktop/modular-alpha && git fetch origin && git log --oneline origin/main -3

# Search for verification markers (bypasses browser cache entirely)
grep -r "VERIFICATION_MARKER" ~/Desktop/modular-alpha/supabase/ ~/Desktop/modular-alpha/src/

# Check which branch has the latest commit (wrong-branch detection)
git branch -r --sort=-committerdate | head -5

# Compare local vs remote (detect missing pulls)
git log --oneline HEAD..origin/main

# Verify a specific file was changed in a commit
gh api repos/ainewfeed1/modular-alpha/commits/<hash> | jq '[.files[].filename]'
```

### PR Management via CLI

```bash
# Create a PR to merge a Lovable feature branch
gh pr create --base main --head lovable/<branch> --title "Merge: <description>" --body "From Lovable implementation"

# Auto-merge a PR
gh pr merge <number> --auto --merge

# View PR checks/status
gh pr checks <number>

# List recent PRs
gh pr list --repo ainewfeed1/modular-alpha --limit 5
```

## When to Use CLI vs Browser

| Scenario | Preferred | Why |
|----------|-----------|-----|
| Check recent commits | CLI (`git log`) | No cache, instant, structured |
| Read function source code | CLI (`cat`) | No stale editor cache |
| Compare commit timestamps | CLI (`gh api`) | Machine-parseable JSON |
| Search for code patterns | CLI (`grep`) | Recursive, fast, no cache |
| Verify migration files exist | CLI (`ls`, `cat`) | Direct filesystem, no UI delays |
| Check deployment status | CLI (`supabase functions list`) | Structured output (if authenticated) |
| View frontend rendering | Browser | Must see actual pixels |
| Interact with Lovable chat | Browser | TipTap editor requires browser |
| Take screenshots for evidence | Browser | Visual proof |
| Run SQL queries on live DB | Browser (SQL Editor) or pgcli | Depends on auth |
| Manage PRs and merges | CLI (`gh pr`) | Faster, scriptable |
