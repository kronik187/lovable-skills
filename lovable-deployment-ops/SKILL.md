---
name: lovable-deployment-ops
description: |
  Post-merge deployment ops via Lovable cloud. Covers 6 tasks: PR merge confirmation,
  SQL migrations, edge function deployment, cloud issue investigation, frontend testing,
  JWT/anon key capture. Includes 4-layer verification (GitHub/Edge Functions/SQL/Frontend),
  9 hallucination detection patterns, Notion recording. NOT for coding — Lovable is ops only.
  Triggers: "verify deployment", "confirm merge", "run migrations", "deploy edge functions",
  "edge function failed", "frontend test", "cloud issue", "capture JWT", "anon key",
  "4-layer verification", "post-merge", "smoke test", "Lovable cloud", "deployment failed",
  "migration didn't run", "function not deploying", "verify after merge".
  Use whenever Lovable is referenced in deployment or operations context.
---

# Lovable Deployment Operations

Lovable is used exclusively as a **deployment and cloud operations platform** — not for writing code or implementing features. All development happens through Claude Code CLI. After a PR merges to `main`, Lovable handles deployment verification, migration execution, and cloud operations.

## Pre-Flight: CDP Connectivity Check (MANDATORY before any browser task)

Before attempting ANY Lovable browser interaction, run this check:

```bash
curl -s --connect-timeout 5 http://localhost:9222/json/version
```

**Expected response:** JSON with `Browser`, `webSocketDebuggerUrl` fields (Chrome on remote Mac via SSH tunnel).

**If this fails:** The SSH tunnel (`chrome-cdp-tunnel.service`) is down. Do NOT attempt browser tasks. Set status to `blocked` and escalate: "CDP tunnel unreachable — board must restart chrome-cdp-tunnel.service on WSL."

**If this succeeds but Playwright MCP tools still fail:**
1. Verify MCP config: `cat ~/.claude/settings.json` — must have `mcpServers.playwright` with args `["-y", "@playwright/mcp@latest", "--cdp-endpoint", "http://localhost:9222"]`
2. Test MCP binary: `npx -y @playwright/mcp@latest --help`
3. If both pass, try a simple `browser_navigate` to `https://example.com` as a connectivity test
4. Only escalate as `blocked` if Step 1 or 2 fail — most Playwright MCP issues are transient

## Lovable's 6 Operational Responsibilities

1. **Confirm PR has merged** — verify merge commit on `main` via GitHub
2. **Run SQL migrations** — execute and verify schema changes via Lovable Cloud SQL Editor
3. **Trigger/verify edge function deployment** — confirm functions deployed and running
4. **Investigate cloud issues** — debug runtime errors, check logs, trace failures
5. **Conduct frontend testing** — verify UI renders correctly post-deployment
6. **Capture JWT tokens** — retrieve Supabase anon key for authentication

Anything outside these 6 areas is NOT Lovable's responsibility. If you need to write code, debug logic, or implement features, use Claude Code CLI directly.

---

## 4-Layer Verification Framework

Every deployment must pass all 4 layers before being marked complete. No shortcuts — single-layer verification has caused real production failures.

### Layer 1: GitHub (Ground Truth)

GitHub is the source of truth. If the diff doesn't show the change, it didn't happen — regardless of what Lovable's preview shows.

```bash
# Quick merge verification
git fetch origin && git log --oneline origin/main -5

# Full diff of last merge
git diff origin/main~1..origin/main --stat

# Verify specific files changed
git diff origin/main~1..origin/main -- <file-path>

# Check CI status
gh api repos/{owner}/{repo}/commits/{sha}/status | jq '.state'

# Detect wrong-branch deployment
git branch -r --sort=-committerdate | head -5
git log origin/main..origin/<feature-branch>
```

**What to verify:** Merge commit exists, target files modified, logic matches the plan, commit timestamp is after the PR was approved.

### Layer 2: Edge Functions

Edge function deployment is the **least reliable** layer. Code may appear committed but never deploy. Always verify with timestamps.

```bash
# List deployed functions
supabase functions list

# View function logs (check for recent invocations)
supabase functions logs <function-name> --limit 20

# Read source directly (bypasses browser cache)
cat ~/Desktop/<project>/supabase/functions/<function-name>/index.ts

# Type-check locally
deno check supabase/functions/<function-name>/index.ts
```

**Browser verification:** Check Edge Functions dashboard for:
- Function exists and status is active
- "Last deployed" timestamp is AFTER the merge commit timestamp
- No orange "Couldn't deploy" error cards

**When edge functions won't deploy:** This is a known Lovable issue. After 5+ minutes with no deployment, do NOT wait indefinitely. Flag it as a blocker and suggest a frontend-side alternative if the logic can be moved to a React component. Frontend deployments are immediate and reliable.

### Layer 3: SQL / Migrations

Verify schema changes actually executed — migration files can exist in git without being run.

```bash
# Check latest migration files
ls -lt ~/Desktop/<project>/supabase/migrations/ | head -5

# Search migrations for table references
grep -rl '<table_name>' ~/Desktop/<project>/supabase/migrations/

# List executed migrations
supabase migration list
```

**SQL verification queries** (run via Lovable Cloud SQL Editor or pgcli):

```sql
-- Schema discovery: verify columns exist
SELECT column_name, data_type, is_nullable
FROM information_schema.columns
WHERE table_schema = 'public' AND table_name = '<table>'
ORDER BY ordinal_position;

-- Verify table exists (catches phantom migrations)
SELECT table_name FROM information_schema.tables
WHERE table_schema = 'public' AND table_name = '<table>';

-- Check RLS is enabled
SELECT tablename, rowsecurity FROM pg_tables
WHERE schemaname = 'public' AND tablename = '<table>';

-- Check RLS policies
SELECT policyname, cmd, qual FROM pg_policies
WHERE tablename = '<table>';

-- Verify migration executed
SELECT * FROM supabase_migrations.schema_migrations
ORDER BY version DESC LIMIT 5;

-- Detect duplicate rows from concurrent syncs
SELECT <unique_columns>, COUNT(*)
FROM <table>
GROUP BY <unique_columns>
HAVING COUNT(*) > 1;

-- Check data integrity: nulls and recency
SELECT COUNT(*) AS total,
       COUNT(<key_column>) AS non_null,
       MAX(updated_at) AS latest_update
FROM <table>;
```

**What to verify:** Tables/columns exist with correct types, RLS enabled and policies configured, migration appears in executed list, no duplicate rows, data looks sane.

### Layer 4: Frontend

Always hard refresh before frontend verification — Lovable serves stale cached code.

```bash
# Build check (catches compile errors)
npm run build 2>&1 | tail -20

# Lint check
npm run lint

# Dependency audit
npm audit --audit-level=high
```

**Browser verification:**
1. Hard refresh: `browser_evaluate(() => location.reload(true))` — NEVER use Cmd+Shift+R (keyboard shortcuts don't work over CDP)
2. Navigate to the affected page
3. Screenshot the result
4. Check browser console for JavaScript errors
5. Test the user flow end-to-end
6. Verify loading, empty, error, and success states all render correctly

### Verification Summary Template

After completing all 4 layers, record results in this format:

```
## Deployment Verification — [Feature/PR Name]
- **PR:** #<number> merged at <timestamp>
- **Layer 1 (GitHub):** ✅/❌ — Merge commit <sha>, files: <list>
- **Layer 2 (Edge Functions):** ✅/❌/⚠️ — Deployed at <timestamp> / NOT deployed
- **Layer 3 (SQL):** ✅/❌ — Migration <version> executed, schema verified
- **Layer 4 (Frontend):** ✅/❌ — UI renders correctly, no console errors
- **Overall:** PASS / FAIL / PARTIAL (specify which layers failed)
```

---

## Hallucination Detection

Lovable can report things that didn't actually happen. These 9 patterns have been observed in production across 70+ deployments. When any verification layer fails, check these patterns before assuming a real bug.

### Pattern 1: Phantom Implementation
Code appears to be written but no commit exists in GitHub.
- **Detect:** `git fetch origin && git log --oneline origin/main -3`
- **Recover:** Re-request the change. Verify through GitHub diff before accepting.

### Pattern 2: Stale Cache False Negative
Code exists but Lovable's editor shows old version.
- **Detect:** Hard refresh via `browser_evaluate(() => location.reload(true))`, then search again. Or bypass browser entirely: `grep -r "TERM" ~/Desktop/<project>/`
- **Recover:** Always hard refresh before verification. Prefer CLI over browser for code checks.

### Pattern 3: Self-Validating Verification
Lovable claims it verified something but only checked syntax, not functionality.
- **Detect:** Read the actual verification steps word-by-word. Did it check the behavior, or just that the file exists?
- **Recover:** Run your own verification commands. Lovable's self-assessment is not evidence.

### Pattern 4: Silent Deployment Failure
Code committed to GitHub but edge function never deployed.
- **Detect:** Compare Edge Functions dashboard "Last deployed" timestamp vs GitHub commit timestamp. If dashboard timestamp is older, deployment failed silently.
- **Recover:** Trigger redeployment. If it persists, pivot to frontend fix.

### Pattern 5: Column Name Hallucination
Lovable assumes column names that don't exist in the actual schema.
- **Detect:** Run `SELECT column_name FROM information_schema.columns WHERE table_name = '<table>'` BEFORE writing any queries.
- **Recover:** Always use schema discovery results. Common traps: `balance_usd` vs `value_usd`, `updated_at` vs `last_updated` vs `sync_updated_at`.

### Pattern 6: CODEGEN_ERROR / Syntax Break
Generated code has syntax errors, visible as rapid-fire fix commits in GitHub.
- **Detect:** Look for "CODEGEN_ERROR" in Lovable chat. Check GitHub for multiple rapid commits to the same file.
- **Recover:** If auto-fixed, verify logic unchanged. If broken, may need manual rewrite.

### Pattern 7: Phantom Migration
Migration file committed but never executed against the database.
- **Detect:** `SELECT table_name FROM information_schema.tables WHERE table_name = '<expected_table>'` — if empty, migration didn't run.
- **Recover:** Copy migration SQL and run manually via SQL Editor. Verify with information_schema.

### Pattern 8: Wrong-Branch Deployment
Code landed on a feature branch, not `main`. Lovable preview may show it, but production won't have it.
- **Detect:** `git branch -r --sort=-committerdate | head -5` — check which branch has the recent commit.
- **Recover:** Merge the feature branch to `main`, wait for deployment, run full 4-layer verification.

### Pattern 9: Concurrent Sync Race Condition
Real race condition (not a hallucination) causing duplicate rows from concurrent operations.
- **Detect:** `SELECT <columns>, COUNT(*) FROM <table> GROUP BY <columns> HAVING COUNT(*) > 1`
- **Recover:** Clean up duplicates (keep most recent), add unique constraint, convert INSERT to UPSERT.

### General Rules
- **Evidence before claims.** "Should work" and "looks correct" are not evidence. Command output is evidence.
- **GitHub diff is ground truth.** If it's not in the diff, it didn't happen.
- **Hard refresh always.** `browser_evaluate(() => location.reload(true))` before every browser-based verification. NEVER use Cmd+Shift+R.
- **Never rely solely on edge functions.** They are the least reliable deployment path.
- **Prefer CLI over browser.** CLI bypasses cache, returns structured output, and is faster.

---

## Post-Deployment Monitoring

After all 4 layers pass, monitor for runtime issues:

1. **Sentry check** — search for new errors correlated with the deployment within 2 minutes
2. **Smoke test** — trigger the primary user flow that the deployment affects
3. **Function logs** — `supabase functions logs <name> --limit 20` for any runtime errors
4. **If new errors appear** — correlate error timestamps with deployment timestamp. Flag immediately rather than investigating — investigation is the Lead Engineer's responsibility.

---

## Recording Results to Notion

After verification completes, record results immediately — before context is lost.

**Update the relevant issue/task page:**
- Set Status property to done (or blocked if verification failed)
- Set Test Passed to true/false
- Set Completed Date to current timestamp
- Add verification summary as a comment or in the page body
- Include screenshots of frontend verification
- Link to the merge commit

**Issue Tracker schema** (if logging new issues discovered during verification):
- Name, Type (bug/task), Status, Priority (critical/high/medium/low), Phase, Logged Date, Completed Date, Test Passed (boolean)

**Batch recording:** When recording multiple verification results, batch in groups of 10 to avoid Notion MCP overload.

---

## JWT Token / Anon Key Capture

When you need to capture the Supabase anon key or JWT token from Lovable:

1. Open the Lovable project in browser
2. Open browser DevTools (F12) → Network tab
3. Trigger any Supabase request (page load, data fetch)
4. Find a request to `*.supabase.co`
5. Copy the `apikey` header value — this is the anon key
6. For JWT tokens, copy the `Authorization: Bearer <token>` header value

Alternatively, check the project's environment configuration in Lovable's settings panel for the `SUPABASE_ANON_KEY` value.

---

## Browser Interaction with Lovable

When using Claude in Chrome MCP tools to interact with Lovable's dashboard:

**Tab management:**
- Use `tabs_context_mcp({ createIfEmpty: true })` to get/create browser tabs
- Verify Lovable tab exists with URL pattern `lovable.dev/projects/`
- If tab disconnects: call `tabs_context_mcp()`, navigate fresh, wait 10s, check if chat sidebar collapsed (use `find("Open sidebar")` to re-expand)

**Key coordinates (verified Feb 2026):**
- Editor focus: click at (150, 419)
- Submit button: click at (287, 494)
- Approve button: ~(234, 387)

**CRITICAL — Zero keyboard shortcuts over CDP:**
This is a remote Chrome on a Mac accessed via CDP over SSH. Keyboard shortcuts get intercepted and trigger unintended actions:
- Ctrl+D / Cmd+D → TOGGLES LOVABLE DARK/LIGHT THEME (not bookmark)
- Ctrl+V / Cmd+V → does NOT paste (clipboard not shared over CDP)
- Ctrl+C / Cmd+C → does NOT copy
- Ctrl+A / Cmd+A → does NOT select all
- Any Ctrl+/Cmd+/Alt+ combo → UNRELIABLE, do not use
**RULE: Never send ANY keyboard shortcut. Use `browser_evaluate` with JavaScript for all non-typing actions.**

**Text input rules:**
- **ALWAYS use JavaScript injection** to enter text in Lovable chat (or any rich input):
  ```javascript
  const editor = document.querySelector('.tiptap.ProseMirror');
  editor.focus();
  editor.innerHTML = '<p>Your prompt here. Use periods not newlines.</p>';
  editor.dispatchEvent(new Event('input', { bubbles: true }));
  ```
  Then click the submit button via `browser_click`.
- Never use newlines — TipTap treats Enter as a separate message submission
- Use periods or semicolons to separate sentences
- **Hard refresh:** `browser_evaluate(() => location.reload(true))` — NOT Cmd+Shift+R
- **Select all:** `browser_evaluate(() => { el.focus(); document.execCommand('selectAll'); })` — NOT Ctrl+A

**MCP constraints:**
- Maximum 30s per wait call (longer causes MCP timeout)
- Element refs from `find` go stale after page interactions — always re-find immediately before clicking
- After any page refresh, Plans may hide above the viewport — scroll UP to check

---

## CLI vs Browser Decision Guide

| Task | Prefer | Reason |
|------|--------|--------|
| Commit verification | CLI (`git log`, `git diff`) | Ground truth, no cache issues |
| Edge function source code | CLI (`cat`, `grep`) | Bypasses stale browser cache |
| Edge function deployment status | Both (CLI `supabase functions list` + browser dashboard timestamps) | CLI for existence, browser for visual timestamp comparison |
| SQL schema verification | CLI (`pgcli`) or Browser (SQL Editor) | Either works — CLI is scriptable |
| Migration execution status | CLI (`supabase migration list`) | Structured output |
| Frontend rendering | Browser only | Visual verification requires rendering |
| Lovable chat interaction | Browser only | No CLI equivalent |
| PR management | CLI (`gh pr`) | Faster, scriptable |
| Error monitoring | Sentry MCP or CLI | Structured, searchable |

---

## What This Skill Does NOT Cover

- Writing code or implementing features (use Claude Code CLI)
- Brainstorming or design specs (use `superpowers:brainstorming`)
- Implementation planning (use `superpowers:writing-plans`)
- Code review (use `superpowers:requesting-code-review`)
- Debugging application logic (use `superpowers:systematic-debugging`)
- Creating Lovable projects or prompts (Lovable is not the development tool)
