---
name: lovable-tech-stack-workflow
description: |
  Implement, debug, and verify code changes across Lovable's Supabase tech stack with 4-layer
  verification (GitHub → Edge Functions → SQL → Frontend), AI hallucination detection, and
  preflight bootstrap. Triggers: "verify Lovable changes", "debug deployment", "edge function
  failed", "couldn't deploy", "4-layer verification", "hallucination check", "stale code",
  "Lovable says it's done but", "schema mismatch", "tech stack verification", "Lovable cloud",
  "check if Lovable actually made the changes", "verify implementation end to end".
  Use whenever working with Lovable's Supabase infrastructure — even for single-layer checks.
  Supplements lovable-notion-automation-workflow, lovable-verification-workflow, and
  lovable-prompt-library. CLI-aware: uses Desktop Commander to run git, gh, supabase, deno
  on the user's Mac for faster verification. See references/cli-environment.md.
---

# Lovable Tech Stack Workflow v4

A systematic approach to implementing, verifying, and debugging code changes across Lovable's full Supabase-backed tech stack. Born from hard-won lessons across 70+ Plans and 200+ monitoring cycles on the CryptoFolio/Mooncatcher project.

**v4 enhancement:** This version is a *coordinator* — it orchestrates a constellation of specialist skills rather than duplicating their knowledge. The 4-layer verification framework, hallucination detection, and Lovable-specific patterns remain the core of this skill. For deep domain work (Postgres optimization, edge function deployment, error tracking, frontend patterns, git workflows), it delegates to purpose-built companion skills that contain richer, more current guidance.

## How to Use This Skill

This skill works by telling you to invoke other skills at specific checkpoints. When you see "Invoke X using the Skill tool," that means literally call the Skill tool with that skill name before continuing. The three non-negotiable checkpoints are:

1. **Before implementation** → invoke `superpowers:brainstorming` (Step 2)
2. **Before claiming verification passes** → invoke `superpowers:verification-before-completion` (Step 3)
3. **Before proposing fixes for failures** → invoke `superpowers:systematic-debugging` (Step 4)

These are not suggestions. Each one exists because skipping it has caused real, costly failures on this project. The specialist skills (`supabase-postgres-best-practices`, `supabase-automation`, etc.) are strongly recommended but situational — use them when the step says to.

## Skill Ecosystem

This skill sits at the center of a hub-and-spoke model. Load it first; it tells you when to reach for the others.

### Lovable Workflow Skills (load during preflight)
| Skill | Role in this workflow |
|-------|---------------------|
| `lovable-notion-automation-workflow` | Plan→Approve→Implement→Verify loop, queue monitoring, TipTap rules |
| `lovable-verification-workflow` | Browser coordinates, monitoring loops, screenshot timing |
| `lovable-prompt-library` | 200+ prompt templates for structuring Lovable requests |

### Technical Specialist Skills (invoke on demand)
| Skill | When to invoke | Replaces |
|-------|---------------|----------|
| `supabase-postgres-best-practices` | Layer 3 — writing verification queries, checking RLS, reviewing indexes, schema design | Inline SQL patterns that were previously duplicated here |
| `supabase-automation` | Layer 2 — edge function deployment, CLI commands, migration management, auth config | Supabase CLI command recipes that lived in references/cli-environment.md |
| `error-tracking` | Step 6 — post-deployment monitoring, structuring Sentry error capture, controller patterns | The basic Sentry check that was a few lines here |
| `senior-frontend` | Layer 4 — React/Vite/Tailwind verification patterns, build diagnostics, component structure | Ad-hoc build check commands |
| `git-advanced-workflows` | Layer 1 — complex branch situations, cherry-picks, bisect for regression hunting, reflog recovery | Branch management recipes scattered through hallucination patterns |
| `github-actions-templates` | CI/CD — setting up automated test/deploy pipelines for the project | Not previously covered |
| `nodejs-backend-patterns` | Edge function architecture — error handling, middleware patterns, API design | Not previously covered |
| `crypto-research` | Token/chain validation — verifying market data accuracy, price feed sanity checks | Crypto-specific schema checks that were inline |

### Process & Discipline Skills (superpowers — govern HOW you work)
| Skill | When to invoke | Discipline it enforces |
|-------|---------------|----------------------|
| `superpowers:brainstorming` | Step 2 — before writing any Lovable prompt for new features or significant changes | Design before implementation; prevents jumping to code without requirements clarity |
| `superpowers:writing-plans` | Step 2 — when a task has multiple implementation steps before sending to Lovable | Structured planning with checkpoints; prevents ad-hoc prompting |
| `superpowers:executing-plans` | Step 2 — when working from an existing plan (e.g., Notion sprint items) | Load plan → review critically → execute with TodoWrite tracking |
| `superpowers:verification-before-completion` | Step 3 — **mandatory** before claiming any layer passes or overall verification succeeds | "Evidence before claims, always" — no completion claims without fresh verification output |
| `superpowers:systematic-debugging` | Step 4 — when any hallucination is detected or verification layer fails | Root cause investigation before fixes; prevents shotgun debugging |
| `superpowers:dispatching-parallel-agents` | Step 3 — when 2+ verification layers can run independently (e.g., CLI git check + SQL check) | Parallel execution of independent work; prevents serial bottlenecks |
| `superpowers:requesting-code-review` | After Step 3 — when implementation is verified and ready for quality review | Dispatch code-reviewer subagent with git SHAs; catches issues humans miss |
| `superpowers:finishing-a-development-branch` | After Step 5 — when all verification passes and work needs to be integrated | Structured merge/PR/cleanup decision; prevents premature or forgotten merges |

The pattern is: this skill tells you *what* to verify and *why*; the specialist skills tell you *how* to do it with best-practice depth; the superpowers skills govern *the discipline of how you work* — ensuring you plan before coding, verify before claiming, debug systematically, and finish cleanly.

---

## How to Run CLI Commands

Every CLI command in this skill runs on the **user's Mac**, not inside the Cowork sandbox. Execute through **Desktop Commander MCP tools** (`start_process`, `interact_with_process`). Never use the Cowork `Bash` tool for `git`, `gh`, `supabase`, `deno` — that runs inside an isolated Linux VM without these tools or credentials.

```
# One-shot command
Desktop Commander → start_process({ command: "cd ~/Desktop/modular-alpha && git log --oneline -5", timeout_ms: 10000 })

# Interactive session
Desktop Commander → start_process({ command: "pgcli <conn_string>", timeout_ms: 5000 })
Desktop Commander → interact_with_process({ pid: <pid>, input: "SELECT * FROM ..." })

# Read file directly
Desktop Commander → read_file({ path: "/Users/agent1/Desktop/modular-alpha/supabase/functions/<name>/index.ts" })
```

The user is authenticated as `kronik187` on GitHub (keyring auth with repo, workflow, read:org, gist scopes). See `references/cli-environment.md` for the full command reference.

---

## Step 0: Preflight Bootstrap

Run this before any implementation or verification work. Skipping preflight is the #1 cause of incomplete verifications.

### 0.1 Load Lovable Workflow Skills

Read these three skill files to activate their workflows:

- `lovable-notion-automation-workflow/SKILL.md` → Plan→Approve loop, queue monitoring, TipTap rules
- `lovable-verification-workflow/SKILL.md` → Browser coordinates, monitoring loops, result capture
- `lovable-prompt-library/SKILL.md` → Prompt templates for structuring requests to Lovable

If a skill isn't found, inform the user — the workflow degrades significantly without them.

### 0.2 Verify Browser Tabs

Use `tabs_context_mcp({ createIfEmpty: true })` and confirm these tabs exist:

| Tab | URL Pattern | Purpose |
|-----|-------------|---------|
| Lovable | `lovable.dev/projects/` | Implementation, preview, code editor |
| GitHub | `github.com/` + repo path | Commit diffs (ground truth) |
| Sentry | `sentry.io/` | Error monitoring post-deployment |

### 0.3 Verify MCP Tool Connectivity

Confirm these tool families are responsive:

- **Claude in Chrome**: `tabs_context_mcp`, `computer`, `find`, `get_page_text`
- **Notion MCP**: `notion-search`, `notion-fetch`, `notion-create-pages`, `notion-update-page`
- **Sentry MCP** (optional): `search_issues`, `get_issue_details`
- **Desktop Commander** (recommended): `start_process`, `interact_with_process`, `read_file`

### 0.3.1 Verify CLI Environment (via Desktop Commander)

Quick environment check:

```bash
gh auth status 2>&1 && echo '---GH_OK---' && \
git -C ~/Desktop/modular-alpha log --oneline -1 && echo '---GIT_OK---' && \
supabase --version && echo '---SUPA_OK---'
```

When Desktop Commander is available, prefer CLI over browser for verification — CLI is immune to the stale-cache problem that plagues Lovable's editor.

### 0.4 Confirm User Protocols

1. **Hard Refresh Protocol**: Cmd+Shift+R before any code editor verification to clear stale cache.
2. **GitHub Diff Protocol**: Check GitHub (or `gh` CLI) for actual commit diffs before trusting Lovable's claims.
3. **Browser Connection Drop Handling**: Wait 15s silently and retry up to 5 times on connection errors.

---

## Step 1: Pre-Implementation Schema Discovery

Before asking Lovable to implement anything touching the database, discover the actual schema. Lovable will hallucinate column names if you assume them.

### Schema Discovery Protocol

Navigate to Lovable Cloud → SQL Editor, or use CLI:

```sql
SELECT column_name, data_type, is_nullable
FROM information_schema.columns
WHERE table_schema = 'public' AND table_name = '<target_table>'
ORDER BY ordinal_position;
```

**CLI alternative:**
```bash
pgcli <connection_string> -e "SELECT column_name, data_type FROM information_schema.columns WHERE table_schema = 'public' AND table_name = '<table>' ORDER BY ordinal_position;"
# Or inspect migration files:
grep -r '<target_table>' ~/Desktop/modular-alpha/supabase/migrations/ | tail -5
```

Record the results before writing any implementation prompt. Reference actual column names in all subsequent prompts to Lovable.

> **Invoke now:** For any schema work beyond basic column lookups (indexing strategy, RLS policy design, partitioning, connection pooling), invoke `supabase-postgres-best-practices` using the Skill tool. It contains 34 reference files with Supabase-specific guidance that prevents common mistakes like missing indexes on foreign keys or overly permissive RLS.

### Common Schema Traps

- `balance_usd` vs `value_usd` — naming varies by table
- `source` column may not exist — check before filtering
- `updated_at` vs `last_updated` vs `sync_updated_at` — timestamp columns are inconsistent
- RLS policies can make tables appear empty from the wrong context

### Crypto-Specific Schema Checks

When working with blockchain/crypto data, verify token decimals (ETH=18, USDC=6, BTC=8), data types (NUMERIC not TEXT for prices/balances), unique constraints for sync data, and foreign key integrity. See `references/sql-verification-templates.md` for ready-to-run queries.

> **Deep dive:** For token price validation and market data sanity checks, invoke the `crypto-research` skill — it can cross-reference on-chain balances against market APIs.

---

## Step 2: Implementation via Lovable

> **⛔ PREREQUISITE — do not skip this.**
> Before writing ANY Lovable prompt for new features or significant changes:
>
> 1. **Invoke `superpowers:brainstorming`** — use the Skill tool right now. This runs a 9-step collaborative design process (intent, requirements, edge cases, trade-offs). It produces a design that the user reviews before any code is written.
> 2. **Invoke `superpowers:writing-plans`** if the task has 2+ implementation steps. This produces a structured plan with checkpoints.
> 3. If working from an existing plan (e.g., Notion sprint items), invoke `superpowers:executing-plans` to load, review, and track it.
>
> The sequence is: **brainstorm → plan → implement**. Each Lovable Plan costs real money and time. Skipping brainstorming is the #1 cause of wasted Plan cycles — the user has burned 10+ Plans on implementations that missed requirements because nobody stopped to think first.
>
> **Self-check:** If you're about to type a prompt to Lovable and you haven't invoked brainstorming, STOP. Go back and invoke it now.

### 2.1 Prompt Structure

Structure requests to minimize hallucination risk. Use the "Create a plan:" prefix (from lovable-notion-automation-workflow) to prevent immediate execution.

1. **Explicit file targets**: Name exact files (e.g., "In supabase/functions/cryptofolio-scheduled-sync/index.ts")
2. **Exact column/function names**: Use names from Step 1, not assumed names
3. **Verification hooks**: Ask Lovable to add console.log or comment markers
4. **Scope boundaries**: Tell Lovable what NOT to change

Verify file paths before referencing them:
```bash
ls ~/Desktop/modular-alpha/supabase/functions/<function-name>/
head -30 ~/Desktop/modular-alpha/supabase/functions/<function-name>/index.ts
```

### 2.2 Implementation Red Flags

- **"I've already implemented this"** without showing a Plan → Likely reading existing code
- **"All checks PASS"** in the same message as implementation → Superficial self-validation
- **Very fast completion** (<30s for complex changes) → May have only modified one file or none
- **"I updated the function"** without showing git diff → Changes may not have saved

---

## Step 3: 4-Layer Verification

The core of this skill. Each layer catches different failure modes. Run all four for every implementation, in order.

> **⛔ PREREQUISITE — do not skip this.**
> Before writing a Verification Summary or claiming ANY layer passes:
>
> 1. **Invoke `superpowers:verification-before-completion`** — use the Skill tool right now. This skill's iron law: **no completion claims without fresh verification evidence.** "Should work," "looks correct," and "I'm confident" are not evidence — command output is evidence. If you haven't run the verification command yourself in this session, you cannot mark a layer as ✅.
> 2. **Consider `superpowers:dispatching-parallel-agents`** — if 2+ layers can run independently (e.g., CLI `git diff` for Layer 1 while running SQL queries for Layer 3), invoke this skill to run them concurrently. Layers 1-3 are typically parallelizable via CLI; Layer 4 (frontend) usually depends on Layers 2-3 passing first.
>
> Why this matters: In 200+ monitoring cycles, the single most common failure mode was "verification claimed to pass based on what Lovable said, not based on fresh evidence." This prerequisite exists because agents (and humans) naturally want to move fast and skip re-checking. The cost is deploying broken code to production.
>
> **Self-check:** Before you write ✅ next to any layer, ask: "Did I run a command and see output that proves this?" If the answer is no, the layer is ❌ until you do.

### Layer 1: GitHub Commit Verification

**What it catches:** Code that was never written, partial implementations, wrong-branch deployments.

**CLI approach (preferred):**
```bash
cd ~/Desktop/modular-alpha && git fetch origin && git log --oneline origin/main -5
git diff origin/main~1..origin/main -- supabase/functions/<function-name>/
```

**Verification checklist:**
1. Commits with messages matching the implementation exist
2. Target files were actually modified; line count delta makes sense
3. Code logic matches the request; no unintended files modified
4. Commit timestamp is after your request (not from a previous attempt)

**Ground truth principle:** If the GitHub diff doesn't show the change, it didn't happen.

> **Deep dive:** For complex branch situations (feature branches, cherry-picks, bisect to find regressions), invoke the `git-advanced-workflows` skill. It covers interactive rebase, worktrees, and reflog recovery.

### Layer 2: Edge Functions Dashboard Verification

**What it catches:** Deployment failures, function crashes, stale deployments.

**CLI approach:**
```bash
supabase functions list
supabase functions logs <function-name> --limit 20
cat ~/Desktop/modular-alpha/supabase/functions/<function-name>/index.ts | grep -n "VERIFICATION_MARKER\|console.log"
deno check supabase/functions/<function-name>/index.ts
```

**Browser fallback:** Check Edge Functions dashboard status indicators, "Last deployed" timestamp, and orange "Couldn't deploy" error cards in Lovable chat.

> **Deep dive:** For edge function deployment automation, migration management, auth configuration, and secrets management, invoke the `supabase-automation` skill. For error handling patterns within edge functions (controller-level capture, severity levels, cron monitoring), invoke the `error-tracking` skill. For edge function architecture decisions (middleware patterns, DI, connection pooling), invoke the `nodejs-backend-patterns` skill.

### Layer 3: SQL Editor Verification

**What it catches:** Missing migrations, incorrect data types, RLS gaps, data integrity issues.

Run these verification categories (see `references/sql-verification-templates.md` for ready-to-use queries):

1. **Schema verification** — confirm new columns/tables/indexes exist
2. **Data verification** — confirm implementation produces expected data
3. **RLS policy verification** — confirm policies exist for new tables
4. **Function verification** — for database functions
5. **Multi-chain data verification** — verify each chain independently for sync operations
6. **Duplicate detection** — check for race condition artifacts after sync operations

> **Invoke now:** Before writing Layer 3 verification queries, invoke `supabase-postgres-best-practices` using the Skill tool. It has dedicated reference files for verification query patterns, index types, RLS performance, and N+1 detection. Writing verification SQL without this skill risks missing issues the queries should catch.

### Layer 4: Frontend Preview Verification

**What it catches:** UI regressions, data display issues, broken user flows.

**CLI pre-check:**
```bash
cd ~/Desktop/modular-alpha && npm run build 2>&1 | tail -20
```

**Browser verification:**
1. Hard refresh (Cmd+Shift+R) — non-negotiable
2. Navigate to the relevant page/component
3. Screenshot and verify: UI elements visible, data displays match SQL results, interactions work
4. Check browser console via `read_console_messages` for JavaScript errors

> **Deep dive:** For React component structure issues, Vite build diagnostics, Tailwind utility class problems, or accessibility concerns, invoke the `senior-frontend` skill. For comprehensive test strategies (unit, integration, e2e), invoke the `javascript-testing-patterns` skill.

### Verification Summary Template

```
## Verification Results — [Feature/Fix Name]

### Layer 1: GitHub ✅/❌
- Commit: [hash] ([+X/-Y] lines)
- Files modified: [list]
- Verified via: [CLI: gh/git | Browser: GitHub tab]

### Layer 2: Edge Functions ✅/❌
- Function: [name] | Status: [Active/Error/Not deployed]
- Last deployed: [timestamp]
- Verified via: [CLI: supabase functions list | Browser: dashboard]

### Layer 3: SQL ✅/❌
- Schema: [columns exist/missing] | Data: [X rows populated / Y total]
- RLS: [policies configured/missing]
- Verified via: [CLI: migration files + pgcli | Browser: SQL Editor]

### Layer 4: Frontend ✅/❌
- UI elements: [visible/missing] | Data display: [correct/incorrect]
- Console errors: [none/list] | Build: [pass/fail]

### Overall: [VERIFIED ✅ / FAILED ❌ — needs re-implementation]
```

---

## Step 4: Hallucination Detection & Recovery

> **⛔ PREREQUISITE — do not skip this.**
> When ANY verification layer fails or a hallucination is detected:
>
> 1. **Invoke `superpowers:systematic-debugging`** — use the Skill tool right now. Follow its four-phase approach: reproduce → isolate → diagnose → fix.
> 2. Do NOT propose a fix until you've completed the diagnosis phase. The most expensive mistake in this workflow is guessing at a fix, burning a Lovable Plan on it, and discovering it was the wrong problem. Root cause first, always.
>
> Why this matters: Across 70+ Plans, shotgun debugging (trying fixes without understanding the root cause) wasted an average of 2.3 additional Plans per incident. Systematic debugging averaged 0.4 additional Plans. The discipline pays for itself immediately.
>
> **Self-check:** If you're about to suggest "try X" and you haven't identified the specific root cause, STOP. Go back to the diagnosis phase.

These patterns are distilled from real incidents where Lovable claimed success that didn't happen. See `references/hallucination-field-guide.md` for the full catalog with real incident details.

### Quick Detection Cheat Sheet

| Pattern | Signal | Fastest Detection |
|---------|--------|-------------------|
| Phantom Implementation | "All changes complete" but no commits | `git fetch && git log --oneline origin/main -3` |
| Stale Cache False Negative | Code search returns nothing | `grep -r "TERM" ~/Desktop/modular-alpha/` (bypasses cache) |
| Self-Validating Verification | Lovable says "All checks PASS" | Read its checks — do they test function or just existence? |
| Silent Deployment Failure | Code in GitHub, old deployment timestamp | Compare `gh api commits/HEAD` date vs dashboard |
| Phantom Migration | Commit references table that doesn't exist | `SELECT table_name FROM information_schema.tables WHERE table_name = '<table>'` |
| Wrong-Branch Deployment | Code on feature branch, not main | `git branch -r --sort=-committerdate | head -5` |

### Recovery Protocol

For any detected hallucination:
1. Document the false claim (screenshot)
2. Re-request with explicit file paths, exact signatures, unique verification markers
3. After re-implementation, verify through GitHub diff first (ground truth)
4. For wrong-branch issues, use `gh pr create` + `gh pr merge` to merge to main

> **Deep dive:** For recovery involving complex git operations (cherry-pick specific commits, bisect to find when a regression was introduced, recover lost work via reflog), invoke the `git-advanced-workflows` skill.

### Edge Cases

**Concurrent Sync Race Condition:** Not a hallucination — scheduled sync overlaps with manual refresh, creating duplicates. Detect with `GROUP BY ... HAVING COUNT(*) > 1`. Fix with upsert logic and unique constraints.

**API Rate Limits / Stale Prices:** External API failures cause edge functions to write 0/NULL values. Detect with `SELECT COUNT(*) FROM table WHERE (price_usd IS NULL OR price_usd = 0) AND updated_at > NOW() - INTERVAL '1 hour'`.

---

## Step 5: Record Results to Notion

After verification, record results using Notion MCP tools:

```
notion-update-page({
  page_id: "<issue-page-id>",
  command: "update_content",
  content_updates: [{ old_str: "<last section>", new_str: "<previous>\n\n## Verification Results\n\n[4-layer results]" }]
})
```

Update properties: Status → "✅ Complete" or "🔨 In Progress", Test Passed → "__YES__" or "__NO__", Completed Date.

### 5.1 Code Review (recommended for major features)

After 4-layer verification passes, invoke `superpowers:requesting-code-review` to dispatch a code-reviewer subagent. Provide the git commit SHAs from Layer 1 so the reviewer can examine actual diffs. This catches architectural issues, security gaps, and maintainability problems that verification alone misses.

### 5.2 Branch Integration

If working on a feature branch (common when Lovable creates `lovable/<branch-name>` branches), invoke `superpowers:finishing-a-development-branch` to make a structured merge/PR/cleanup decision. This skill presents options (merge to main, create PR for review, defer) and handles the git mechanics. For CLI-based merging: `gh pr create --base main --head lovable/<branch> && gh pr merge <number> --merge`.

---

## Step 6: Post-Deployment Monitoring

After successful verification, monitor for runtime errors.

### Sentry Check

```
search_issues({ organizationSlug: "tea-qb", naturalLanguageQuery: "errors in <project> from last hour", regionUrl: "https://us.sentry.io" })
```

> **Deep dive:** For structuring error capture in edge functions (controller-level error handling, Sentry v8 instrumentation, severity levels, cron job monitoring, database performance tracking), invoke the `error-tracking` skill. It provides Sentry-specific patterns for Express middleware, route-level capture, and N+1 detection.

### Smoke Test

1. Trigger the user flow that calls the edge function via Lovable preview
2. Check Sentry for new errors within 2 minutes
3. Check function logs: `supabase functions logs <function-name> --limit 20`

### CI/CD Pipeline Check

> **Deep dive:** If the project needs automated test/deploy workflows, invoke the `github-actions-templates` skill to set up GitHub Actions for test matrix builds, Docker builds, security scanning, and deployment with approval gates.

---

## Quick Reference

### Verification Order
```
1. GitHub commits (ground truth) → CLI preferred: git fetch + git diff
2. Edge Functions dashboard (deployment) → CLI: supabase functions list
3. SQL Editor (data layer) → CLI: migration files + pgcli
4. Frontend Preview (user-facing) → Browser only, CLI build pre-check
```

### Before Every Verification
- [ ] Hard refresh browser (Cmd+Shift+R)
- [ ] Check GitHub for recent commits
- [ ] Run schema discovery on relevant tables
- [ ] If Desktop Commander available, pull latest: `cd ~/Desktop/modular-alpha && git pull`

### Hallucination Red Flags
- "Already implemented" without showing changes
- "All checks PASS" immediately after implementation
- No new GitHub commits after claimed implementation
- Edge function "Last deployed" timestamp is old
- Code search returns no results (cache issue — use CLI `grep`)
- "Migration ran successfully" but table doesn't exist
- Changes on feature branch instead of main

### Companion Skills Quick Reference
| Need | Invoke |
|------|--------|
| SQL query optimization, RLS, indexes | `supabase-postgres-best-practices` |
| Edge function deploy, CLI, migrations | `supabase-automation` |
| Sentry setup, error capture patterns | `error-tracking` |
| React/Vite/Tailwind diagnostics | `senior-frontend` |
| Complex git operations, recovery | `git-advanced-workflows` |
| CI/CD pipeline setup | `github-actions-templates` |
| Edge function architecture | `nodejs-backend-patterns` |
| JS/TS test strategy | `javascript-testing-patterns` |
| Token/market data validation | `crypto-research` |
| Design before implementation | `superpowers:brainstorming` |
| Structured multi-step planning | `superpowers:writing-plans` / `superpowers:executing-plans` |
| Verification discipline (evidence before claims) | `superpowers:verification-before-completion` |
| Root cause debugging (no guessing) | `superpowers:systematic-debugging` |
| Run independent work in parallel | `superpowers:dispatching-parallel-agents` |
| Post-implementation code review | `superpowers:requesting-code-review` |
| Merge/PR/cleanup decisions | `superpowers:finishing-a-development-branch` |
