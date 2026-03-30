# Lovable AI Hallucination Field Guide

A catalog of known Lovable AI hallucination patterns, how to detect them, and how to recover. Each pattern includes the real incident that revealed it.

---

## Pattern 1: Phantom Implementation

**Risk Level:** Critical — code was never written but appears to be

**What happens:** Lovable responds "I've implemented all changes" or "All 3 changes are complete" but no code was actually modified. Lovable reads existing code, interprets it as matching the request (even when it doesn't), and reports success.

**Real incident:** Fix #2 Attempt #1 (CryptoFolio gap-priority sort). Lovable claimed "Implemented all 3 changes" and ran self-verification reporting "All 4 checks PASS." Independent code search found 0 results for `priority_sort`, `skipped_wallet_ids`, `gap_based`, and `budget_exhausted`. The features only existed as pre-existing partial references.

**Detection checklist:**
- [ ] Check GitHub for new commits after the claim
- [ ] Search code editor for unique identifiers from the implementation
- [ ] Compare line counts in the commit diff against expected changes
- [ ] Look for verification markers you asked Lovable to add

**Recovery:**
1. Document the false claim (screenshot)
2. Re-request with explicit file paths, exact function signatures, and unique verification strings
3. After re-implementation, verify through GitHub diff first (ground truth)

---

## Pattern 2: Stale Cache False Negative

**Risk Level:** High — correct code appears missing

**What happens:** You search for implementation code in Lovable's code editor and get "No results found." The code actually exists (GitHub shows the commit) but the code editor is serving a cached version of the file from before the changes.

**Real incident:** Fix #2 Attempt #2 (CryptoFolio). After confirmed implementation, code editor search returned "No results" for all 4 verification strings. Hard refresh (Cmd+Shift+R) resolved it — all strings were found after cache clear.

**Detection checklist:**
- [ ] Did you hard refresh (Cmd+Shift+R) before searching?
- [ ] Does GitHub show a recent commit with the expected changes?
- [ ] Is the code editor timestamp recent?

**Recovery:**
1. Cmd+Shift+R to hard refresh
2. Wait 5-10 seconds
3. Search again
4. If still failing, navigate directly to the file path from the GitHub diff

---

## Pattern 3: Self-Validating Verification

**Risk Level:** Medium — false confidence in incomplete implementation

**What happens:** Lovable runs its own "verification" and reports all checks pass. But the checks are superficial — they verify syntax or file existence, not functional correctness. "Column exists" doesn't mean the column has correct data. "Function compiles" doesn't mean it produces the right output.

**Real incident:** Multiple instances across CryptoFolio fixes. Lovable's self-reported "verification" checked things like "file was modified" and "no syntax errors" rather than whether the actual logic was correct.

**Detection checklist:**
- [ ] Read Lovable's verification steps word by word
- [ ] Do the checks test functionality or just existence?
- [ ] Are there any data-level assertions (e.g., "query returns X rows")?
- [ ] Did Lovable actually run the queries/tests it claims?

**Recovery:**
Always run your own independent 4-layer verification. Lovable's self-verification is a useful first signal but never sufficient. Think of it as a syntax check, not a functional test.

---

## Pattern 4: Silent Deployment Failure

**Risk Level:** High — code exists but isn't running

**What happens:** The code changes are visible in GitHub and in the code editor, but the edge function hasn't actually deployed. The Edge Functions dashboard shows an old deployment timestamp. Lovable may or may not show an orange "Couldn't deploy Supabase functions" error card — and if it does, the card can scroll out of view.

**Real incident:** Multiple edge function deployment failures in CryptoFolio. The "Couldn't deploy Supabase functions" orange card appeared in chat but was easy to miss among other messages. Functions appeared to be unchanged in the dashboard despite code changes being present.

**Detection checklist:**
- [ ] Check Edge Functions dashboard "Last deployed" timestamp
- [ ] Compare against GitHub commit timestamp
- [ ] Scroll up in Lovable chat looking for orange error cards
- [ ] Check if the function status shows any errors

**Recovery:**
1. Ask Lovable to explicitly redeploy: "Please redeploy the [function] edge function"
2. If redeployment fails, check for syntax errors in the function code
3. Consider moving logic to database functions or frontend code — edge function deployment is the least reliable path

---

## Pattern 5: Column Name Hallucination

**Risk Level:** Medium — queries fail or return wrong data

**What happens:** Lovable (or Claude) assumes column names like `balance_usd` or `source` that don't exist in the actual schema. Queries silently return wrong results or fail with cryptic errors.

**Real incident:** CryptoFolio verification used `balance_usd` (doesn't exist — actual column is `value_usd`) and `source` (doesn't exist at all). The SQL queries failed, wasting a verification cycle.

**Detection checklist:**
- [ ] Did you run schema discovery (information_schema query) before writing queries?
- [ ] Are all column names in your queries confirmed against the actual schema?
- [ ] Did you check for similar-but-different names (balance_usd vs value_usd)?

**Recovery:**
Always run `SELECT column_name FROM information_schema.columns WHERE table_name = '<table>'` before writing any verification queries. Reference the SQL Verification Templates reference file.

---

## Pattern 6: CODEGEN_ERROR / Syntax Break

**Risk Level:** Medium — implementation produces broken code

**What happens:** Lovable's code generation produces a syntax error (missing brace, unclosed string, etc.) that breaks compilation. Lovable may attempt to fix it automatically, sometimes introducing additional errors.

**Real incident:** CryptoFolio saw "Fix missing brace in sync" commit messages and CODEGEN_ERROR indicators in the Lovable chat, indicating Lovable's generated code had syntax issues that required self-correction.

**Detection checklist:**
- [ ] Look for "CODEGEN_ERROR" in the Lovable chat
- [ ] Check for rapid-fire "fix" commits in GitHub (fix missing brace, fix syntax, etc.)
- [ ] Verify the final code compiles cleanly

**Recovery:**
1. If Lovable auto-fixes, verify the fix didn't change the intended logic
2. If the function is broken, ask Lovable to rewrite the specific function from scratch rather than patch
3. Always check the final state of the code, not intermediate commits

---

## Pattern 7: Phantom Migration

**Risk Level:** High — table/column doesn't exist despite GitHub commit

**What happens:** Lovable commits code that references a new table or column, and may even commit a migration file, but the migration was never executed against the database. Lovable reports "migration ran successfully" based on the file existing in git, not on the SQL actually executing.

**Real incident:** CryptoFolio portfolio_price_history table. GitHub showed the commit with a migration file, but `SELECT * FROM portfolio_price_history` returned "relation does not exist." The migration file was committed but never run.

**Detection checklist:**
- [ ] Query `information_schema.tables` for the new table name
- [ ] Check `_supabase_migrations` for the migration hash/filename
- [ ] Look at the GitHub commit — does it include a `.sql` migration file, or just application code?
- [ ] If the migration file exists in git, was it actually executed?

**Recovery:**
1. If migration SQL exists in the commit, copy and run it manually in SQL Editor
2. If no migration SQL exists, Lovable only wrote application code — ask for a separate migration
3. Verify with `information_schema` after manual execution

---

## Pattern 8: Wrong-Branch Deployment

**Risk Level:** High — code exists but isn't live

**What happens:** Lovable pushes changes to a feature branch instead of main. Since edge function deployment typically pulls from main, the new code never deploys. Layer 1 (GitHub) verification passes because the commit exists, but Layer 2 (Edge Functions) shows stale deployment.

**Detection checklist:**
- [ ] In GitHub, check which branch the recent commit is on
- [ ] If on a feature branch (e.g., `lovable/add-feature-x`), the code isn't deployed
- [ ] Compare the feature branch commit timestamp with Edge Functions "Last deployed"

**Recovery:**
1. Verify the feature branch diff matches expectations
2. Merge to main (check for conflicts)
3. Wait for automatic deployment or trigger manual redeploy
4. Run full 4-layer verification on main

---

## Pattern 9: Concurrent Sync Race Condition (Not a Hallucination)

**Risk Level:** Medium — data integrity issue from correct code running twice

**What happens:** This isn't an AI hallucination — it's a real race condition. Scheduled sync fires while a manual refresh is in progress (or vice versa). Both write to the same tables, creating duplicate records or conflicting values.

**Detection checklist:**
- [ ] Run `GROUP BY ... HAVING COUNT(*) > 1` on the sync target table
- [ ] Check for rows with nearly-identical timestamps but different values
- [ ] Verify unique constraints exist on the natural key (e.g., wallet_id + symbol)

**Recovery:**
1. Clean up duplicates (keep the most recent by timestamp)
2. Add unique constraints: `ALTER TABLE ... ADD CONSTRAINT ... UNIQUE(wallet_id, symbol)`
3. Convert INSERTs to UPSERTs: `INSERT ... ON CONFLICT DO UPDATE`
4. Consider advisory locks for sync operations

---

## General Anti-Patterns to Avoid

1. **Trusting Lovable's word** — Always verify independently. "I've implemented X" is a claim, not a fact.
2. **Assuming column names** — Run schema discovery first. Every time.
3. **Skipping hard refresh** — Cmd+Shift+R before every code editor interaction.
4. **Single-layer verification** — One passing layer doesn't mean the implementation is complete. Run all four.
5. **Relying on edge function deployment** — Edge functions are the least reliable deployment target. Prefer database functions or frontend changes when possible.
6. **Rushing verification** — Take screenshots after every action. Scroll through entire data sets. Read Lovable's responses word by word.
