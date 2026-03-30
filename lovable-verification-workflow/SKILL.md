---
name: lovable-verification-workflow
description: |
  Automated browser-based verification workflow for Lovable implementations with Notion integration.
  Battle-tested across 40+ Plans and 200+ monitoring cycles on the CryptoFolio/Mooncatcher project.
  Triggers: "verify implementation", "run verification", "test in Lovable", "verification loop",
  "browser verification", "automated testing", "check implementation", "PASS/FAIL results",
  "verification plan", "record to Notion", "QA test", "acceptance testing".
  Use when automating Lovable verification tests via Claude in Chrome browser tools while
  recording results to Notion. Works hand-in-hand with the lovable-notion-automation-workflow skill.
---

# Lovable Browser Verification Workflow v2

Automate implementation verification in Lovable using Claude in Chrome browser tools, with results recorded to Notion. This skill focuses on the browser interaction mechanics — for full project management workflow, see the lovable-notion-automation-workflow skill.

**v2 Changelog (from v1):**
- Replaced JavaScript-based editor interaction with coordinate-based + `find` tool fallback
- Added TipTap single-line requirement (CRITICAL — newlines submit separate messages)
- Added tab disconnection recovery protocol
- Added queue-aware verification (verify during queue processing)
- Updated monitoring with MCP timeout constraints (30s max)
- Added production-proven coordinates table
- Added Notion MCP bug workaround for result recording

---

## Prerequisites

- Claude in Chrome MCP tools enabled
- Notion MCP tools enabled
- Active Lovable project tab in browser
- Familiarity with lovable-notion-automation-workflow skill (companion skill)

---

## Core Workflow

```
1. Get browser context → Identify Lovable tab
2. Send verification request to Lovable chat (single-line!)
3. Monitor test execution (screenshot loop, 30s max waits)
4. Capture PASS/FAIL results
5. Record to Notion
```

---

## Step 1: Get Browser Context

```
tabs_context_mcp({ createIfEmpty: true })
```

Identify the Lovable tab by URL pattern (`lovable.dev/projects/`). Record the tabId for all subsequent operations.

**If no tabs found or tab disconnected:**
1. `tabs_context_mcp({ createIfEmpty: true })` — creates new tab group
2. Navigate to Lovable project URL
3. Wait 10s for page load
4. Scroll to bottom of chat
5. Perform hidden Plan scroll check (scroll up, then down)

---

## Step 2: Send Verification Request

### Editor Interaction (Coordinate-Based)

Lovable uses a TipTap/ProseMirror editor. The production-proven approach is coordinate-based clicks, NOT JavaScript injection.

**Production-Proven Coordinates (Feb 2026):**
| Element | Coordinates | Notes |
|---------|------------|-------|
| Editor focus | (150, 419) | Click to focus TipTap editor |
| Submit button | (287, 494) | Send arrow button |
| Approve button | ~(234, 387) | Plan approval; may shift |

**Correct Pattern:**
1. Click editor at ~(150, 419) to focus
2. Use `type` action with **SINGLE-LINE text only**
3. Click submit at ~(287, 494)

```
computer({ action: "left_click", coordinate: [150, 419], tabId: TAB_ID })
computer({ action: "type", text: "Create a plan: Create a verification plan for [FEATURE]. Test categories: 1) Database schema verification 2) UI component rendering 3) User flow validation 4) Error handling. For each item specify: check description, expected result, how to verify.", tabId: TAB_ID })
computer({ action: "left_click", coordinate: [287, 494], tabId: TAB_ID })
```

### CRITICAL: TipTap Newline Bug

Newlines (`\n`) in the `type` action cause SEPARATE message submissions. Each `\n` acts as Enter, which TipTap interprets as "submit message." A multi-line prompt becomes multiple fragmented messages.

**Always:** Single-line continuous text with periods/semicolons for separation.
**Never:** Multi-line text with `\n` characters.

### Button Click Fallback

If coordinate clicks don't register (screenshot shows button still present after 5s), use the `find` tool:

```
find({ query: "submit button", tabId: TAB_ID })
// Returns refs like ref_123
computer({ action: "left_click", ref: "ref_123", tabId: TAB_ID })
```

This fallback pattern works for: submit buttons, Approve buttons, "start preview" buttons, and any interactive element.

---

## Step 3: Verification Plan Templates

### Request Plan (Single Line)
```
Create a plan: Create a verification plan for [FEATURE NAME]. Test categories: 1) Database schema verification 2) UI component rendering 3) User flow validation 4) Error handling. For each item specify: check description, expected result, how to verify (browser test or code inspection). After planning, wait for my approval before executing.
```

### Execute Verification (Single Line)
```
Yes, please execute the verification plan now. Test all components and report PASS or FAIL for each item in the checklist. Provide a summary when complete.
```

### Data Integrity Verification (Single Line)
```
Create a plan: Verify data integrity for [COMPONENT]. Check: 1) All required database columns exist and have correct types 2) RLS policies are properly configured 3) Edge functions handle errors correctly 4) UI displays data accurately. Report PASS/FAIL for each check.
```

---

## Step 4: Monitor Execution

### Status Indicators
| Status | Meaning | Action |
|--------|---------|--------|
| "Thinking" / "Thought for Xs" | Processing | Wait |
| "Creating plan..." | Generating plan | Wait for completion |
| "Waiting for approval" | Plan ready | Review and approve |
| "Running plan in progress" | Tests active | Monitor |
| "Verifying..." | Test in progress | Monitor |
| "X tools used" | Completed | Review results |
| "Stopped" | Execution halted | Re-request |
| "start preview" button | Direct edit done | Click to load preview |

### Checklist Icons
- Circle (○) = Pending
- Filled circle with check = Complete

### Monitoring Loop

```
LOOP:
  1. Take screenshot
  2. Check for completion status or PASS/FAIL results
     → If complete: Proceed to Step 5
  3. Check for Plan awaiting approval
     → If found: Review and click Approve at ~(234, 387)
  4. Check for "Stopped" status
     → If stopped: Re-send execution request
  5. Wait 30 seconds (NEVER exceed 30s — causes MCP timeout)
  6. Take screenshot
  7. REPEAT
```

**Key Rules:**
- Maximum 30s per `wait` call — longer causes MCP timeouts
- Always take screenshot AFTER actions to verify they worked
- Scroll up periodically to check for hidden Plans (especially after page refresh)

---

## Step 5: Capture Results

### Result Request (Single Line)
```
Please provide a summary of the verification test results. List which tests have PASSED and which have FAILED. For any failures, describe the issue and suggested fix.
```

### Expected Result Format
```
## Verification Results

### Database Schema - X/X PASS
- [x] Column exists: column_name
- [x] RLS policy configured

### UI Components - X/X PASS
- [x] Component renders correctly
- [x] Navigation works

### FAILED Items
- [ ] [Description of failure]
  - Issue: [What went wrong]
  - Fix needed: [Suggested resolution]
```

### Reading Results from Chat
After Lovable responds with results:
1. Take screenshot to capture visible results
2. Scroll down if results extend beyond viewport
3. Take additional screenshots as needed
4. Use `get_page_text` if results are very long

---

## Step 6: Record to Notion

### Create Verification Result Page
```javascript
notion-create-pages({
  pages: [{
    properties: { title: "Feature Name - Verification Results" },
    content: "# Feature Name\n\n## Status: Verified ✅\n\n## Verification Results\n[PASS/FAIL counts]\n\n## Details\n[Per-test results]\n\n*Verified: [DATE]*"
  }]
})
```

### KNOWN BUG: Notion MCP `parent` Parameter
The `parent` parameter in `notion-create-pages` doesn't work due to a JSON schema bug — objects get stringified. Pages are created at workspace level.

**Workaround:** Create pages without parent (workspace level), then inform user they need to manually move them into the appropriate database or page.

### Update Existing Page
```javascript
notion-update-page({
  data: {
    page_id: "PAGE_ID",
    command: "replace_content",
    new_str: "[Updated content with verification results]"
  }
})
```

### Batch Recording Pattern
When recording many verification results (e.g., during a fix sprint), create in batches of 10:
```javascript
notion-create-pages({
  pages: [
    { properties: { title: "Fix #1: [Title]", Status: "Complete", "Test Passed": "__YES__" } },
    { properties: { title: "Fix #2: [Title]", Status: "Complete", "Test Passed": "__NO__" } },
    // ... up to 10
  ]
})
```

---

## Error Recovery

### Verification Keeps Getting Cancelled
If tests stop before completing:
1. Take screenshot to see last status
2. Scroll up to check for error messages
3. Re-send execution request as single-line text
4. Monitor more frequently (every 20s)

### Tab Disconnected During Verification
1. `tabs_context_mcp()` — check if tabs exist
2. If no tabs: `tabs_context_mcp({ createIfEmpty: true })`
3. Navigate to Lovable project URL
4. Wait 10s for load
5. Scroll to bottom of chat
6. Check if verification was still running or completed
7. Re-send verification request if needed

### Button Not Responding
When coordinate clicks don't register:
```
// Use find tool to get precise ref
find({ query: "Approve button", tabId: TAB_ID })
// Click using ref instead of coordinates
computer({ action: "left_click", ref: "ref_XXX", tabId: TAB_ID })
```

Works for: Approve, Submit, "start preview", "Back to latest", and any button.

### Editor Not Accepting Input
If clicking editor at (150, 419) doesn't focus:
1. Try `find({ query: "Ask Lovable", tabId: TAB_ID })` to get editor ref
2. Click the ref to focus
3. Then use `type` action

### Message Not Submitting
If clicking submit at (287, 494) doesn't send:
1. `find({ query: "send button", tabId: TAB_ID })`
2. Click the returned ref
3. If still failing, try pressing Enter key:
   ```
   computer({ action: "key", text: "Return", tabId: TAB_ID })
   ```

---

## Queue-Aware Verification

When running verification while the queue is processing other items:

1. Wait for current queue item to complete
2. Submit verification request
3. Your request enters the queue
4. Monitor both queue progress and verification results
5. Queue items may interleave with your verification

**Tip:** Check queue widget for count. If queue has many items, your verification request processes after all preceding items complete.

---

## Best Practices

1. **Always prefix with "Create a plan:"** — prevents immediate execution
2. **SINGLE LINE text only** — newlines fragment messages in TipTap
3. **Copy plans to Notion before approving** — preserves verification criteria
4. **Wait for explicit PASS/FAIL** — don't assume success
5. **Take screenshots after every action** — verify before proceeding
6. **Use `find` tool as button click fallback** — coordinates can miss
7. **Max 30s per wait call** — prevents MCP timeouts
8. **Scroll up after page refresh** — Plans can hide above viewport
9. **Re-run verification if interrupted** — tests must complete fully
10. **Record results to Notion promptly** — before context is lost

---

## Quick Reference

| Action | Method | Notes |
|--------|--------|-------|
| Get tabs | `tabs_context_mcp` | Use `createIfEmpty: true` |
| Screenshot | `computer({ action: "screenshot" })` | After every action |
| Focus editor | `left_click` at (150, 419) | Or use `find` |
| Enter text | `type` action — SINGLE LINE | Never use `\n` |
| Submit | `left_click` at (287, 494) | Or `find` + ref |
| Approve Plan | `left_click` at ~(234, 387) | Or `find` + ref |
| Scroll | `computer({ action: "scroll" })` | Check for hidden Plans |
| Wait | `computer({ action: "wait", duration: 30 })` | Max 30s! |
| Read page text | `get_page_text` | For long results |
| Record to Notion | `notion-create-pages` | Pages go to workspace level |
| Update Notion | `notion-update-page` | Use page_id + command |
