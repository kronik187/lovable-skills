# CLI Verification Commands

All commands run on the user's local machine via Desktop Commander MCP tools (`start_process`, `interact_with_process`, `read_file`). Never use the Cowork sandbox Bash tool for these — it's an isolated Linux VM without credentials or project access.

## Environment

- GitHub auth: `kronik187` (keyring, scopes: gist, read:org, repo, workflow)
- Tools: Git, GitHub CLI (`gh`), Node.js, Supabase CLI, Deno, jq, pgcli
- Supabase CLI requires `supabase login` if not already authenticated

## Layer 1: GitHub Verification

```bash
# Quick merge check
git fetch origin && git log --oneline origin/main -5

# Full diff of last merge
git diff origin/main~1..origin/main --stat

# Verify specific file changed
git diff origin/main~1..origin/main -- <file-path>

# Check CI/CD status for a commit
gh api repos/{owner}/{repo}/commits/{sha}/status | jq '.state'

# List open PRs
gh pr list

# Check PR status/checks
gh pr checks <number>

# Detect wrong-branch deployment
git branch -r --sort=-committerdate | head -5
git log origin/main..origin/<feature-branch>

# Verify specific commit files
gh api repos/{owner}/{repo}/commits/<hash> | jq '[.files[].filename]'
```

## Layer 2: Edge Functions Verification

```bash
# List all deployed functions
supabase functions list

# View recent function logs
supabase functions logs <function-name> --limit 20

# Read function source directly (no cache)
cat ~/Desktop/<project>/supabase/functions/<function-name>/index.ts

# Search for specific code patterns
grep -r "SEARCH_TERM" ~/Desktop/<project>/supabase/functions/

# Type-check function locally
deno check supabase/functions/<function-name>/index.ts
```

## Layer 3: SQL / Migration Verification

```bash
# List latest migration files
ls -lt ~/Desktop/<project>/supabase/migrations/ | head -5

# Search migrations for table references
grep -rl '<table_name>' ~/Desktop/<project>/supabase/migrations/

# Find CREATE TABLE statements
grep -l 'CREATE TABLE' ~/Desktop/<project>/supabase/migrations/

# Check for upsert patterns
grep -r 'ON CONFLICT' ~/Desktop/<project>/supabase/migrations/

# Check for RLS policies in migrations
grep -r 'CREATE POLICY' ~/Desktop/<project>/supabase/migrations/

# List executed migrations
supabase migration list

# Direct database connection for queries
pgcli <connection_string>
```

## Layer 4: Frontend Verification

```bash
# Build check (catches compile errors)
npm run build 2>&1 | tail -20

# Lint check
npm run lint

# Search for error patterns
grep -r "console.error\|throw new Error" ~/Desktop/<project>/src/

# Dependency audit
npm audit --audit-level=high
```

## Hallucination Detection Commands

```bash
# Phantom implementation check
git fetch origin && git log --oneline origin/main -3

# Bypass browser cache entirely
grep -r "VERIFICATION_MARKER" ~/Desktop/<project>/

# Wrong-branch detection
git branch -r --sort=-committerdate | head -5

# Compare local vs remote
git log HEAD..origin/main

# Verify a specific file was part of a commit
gh api repos/{owner}/{repo}/commits/<hash> | jq '[.files[].filename]'
```

## PR Management

```bash
# Create PR from feature branch
gh pr create --base main --head lovable/<branch-name>

# Auto-merge PR
gh pr merge <number> --auto --merge

# View PR checks
gh pr checks <number>

# View PR diff
gh pr diff <number>
```

## Desktop Commander Usage

```
# One-shot command
start_process({ command: "cd ~/Desktop/<project> && git log --oneline -5", timeout_ms: 10000 })

# Interactive session (e.g., pgcli)
start_process({ command: "pgcli <conn_string>", timeout_ms: 5000 })
interact_with_process({ pid: <pid>, input: "SELECT * FROM ..." })

# Read file directly (no process needed)
read_file({ path: "/Users/<user>/Desktop/<project>/supabase/functions/<name>/index.ts" })
```
