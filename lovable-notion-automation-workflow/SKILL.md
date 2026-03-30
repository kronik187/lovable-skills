---
name: lovable-notion-automation-workflow
description: |
  Use this skill when helping users build apps with Lovable (AI app builder) while managing
  projects in Notion. Triggers: "implement project", "test project", "QA the project",
  "Lovable", "phase 1/2/3", "health check", "bug fix workflow", building apps with AI,
  project management automation, "auto approve", "monitor queue", "approve plans",
  "fix sprint", "data integrity", or when user mentions both Lovable and Notion together.
  This skill teaches strict execution discipline for Lovable prompts, queue monitoring,
  Plan auto-approval workflows, and Notion integration. Battle-tested across 70+ Plans
  and 200+ monitoring cycles on the CryptoFolio/Mooncatcher project.
---

# Lovable + Notion Automation Workflow v7

## Overview

Complete workflow automation for AI-assisted app development using Lovable with Notion for project management. Enforces strict execution discipline to prevent common failures.

**Core Philosophy:** Plan → Approve → Implement → Verify → Loop

**v7 Changelog (from v6):**
- MERGED: Combined two divergent v6 branches into single master skill
- Unified lesson numbering (Lessons 8–14) from both branches
- Includes edge function unreliability lesson AND JS clipboard paste method
- Includes Chat Mode plan handling AND console tracking pattern
- Includes button ref staleness, fix sub-numbering, `+` button warning, submit button labels, sidebar collapse
- Notion MCP parent parameter confirmed FIXED — uses `data_source_id` directly
- Updated battle-tested count to 70+ Plans

**v6 Changelog (from v5):**
- CRITICAL: Edge function deployment unreliability lesson (prefer frontend fixes)
- FIXED: Notion MCP parent parameter now works — pages create directly in databases
- Added JavaScript clipboard paste method for long prompts (>2000 chars)
- Added Chat Mode plan handling — Lovable sometimes asks "Shall I proceed?" in text
- Added `+` button warning — opens dropdown menu, not submit
- Added submit button discovery patterns — labels vary across page loads
- Added console tracking for desktop preview testing
- Added button ref staleness and re-find pattern
- Added fix sub-numbering convention for pivoted approaches
- Added sidebar collapse recovery to Tab Disconnection Recovery

**v5 Changelog (from v4):**
- Added Queue Monitoring & Auto-Approval Protocol (Section 4) — battle-tested across 40+ Plans
- Added Critical Browser Interaction Lessons (Section 5) — TipTap newline bug, scroll verification
- Added Notion MCP Known Bugs & Workarounds (Section 11)
- Added Session Continuation Protocol (Section 12) — context preservation across sessions
- Updated Lovable Interface Protocols with production-proven coordinates and patterns
- Added CryptoFolio/Mooncatcher-specific architecture reference

---

## Table of Contents

1. [Non-Negotiable Rules](#non-negotiable-rules)
2. [Main Commands](#main-commands)
3. [Lovable Interface Protocols](#lovable-interface-protocols)
4. [Queue Monitoring & Auto-Approval Protocol](#queue-monitoring--auto-approval-protocol)
5. [Critical Browser Interaction Lessons](#critical-browser-interaction-lessons)
6. [Real-Time Feature Testing](#real-time-feature-testing)
7. [Security Audit Workflow](#security-audit-workflow)
8. [Message Templates](#message-templates)
9. [Verification Protocols](#verification-protocols)
10. [Error Recovery](#error-recovery)
11. [Bug Workflow](#bug-workflow)
12. [Notion Structure & Known Bugs](#notion-structure--known-bugs)
13. [Session Protocols & Continuation](#session-protocols--continuation)

---

## Non-Negotiable Rules

### Rule 1: Always Prefix with "Create a plan"
Every message to Lovable MUST begin with "Create a plan:" - no exceptions.

```
CORRECT: "Create a plan: Add authentication to the dashboard..."
WRONG: "Add authentication to the dashboard..."
```

### Rule 2: Plan-Only Mode Until Approved
NEVER approve implementation until:
1. You have reviewed the complete plan
2. You have copied the plan to Notion
3. You have verified it matches project requirements

### Rule 3: One Full Verified Message — SINGLE LINE ONLY
- Compose messages externally first
- Paste ENTIRE message into Lovable
- **CRITICAL: ALL prompts must be single-line continuous text** (see Section 5)
- **For long prompts (>2000 chars): Use JavaScript clipboard paste** (see Lesson 8) — the `type` action will timeout after 30s on long text
- Visually verify the complete message before submitting
- Never type directly into Lovable unless using the `type` action with single-line text

### Rule 4: Notion MCP Connection
At session start, verify Notion connectivity with a test search. If unavailable, request manual URL/content from user.

### Rule 5: Existing Work Protection
When Phase 1+ already exists:
- Do NOT rebuild existing features
- Only fill gaps from incomplete phases
- Verify what exists before proposing changes

### Rule 6: Never Test App Yourself for QA
During QA, you do NOT test the app yourself. You INSTRUCT Lovable to test itself using its browser capabilities.

> **Note:** For browser-based automated verification (driving the Lovable UI via Claude in Chrome MCP tools and recording results to Notion), see the companion **lovable-verification-workflow** skill.

### Rule 7: Leverage Suggestion Buttons
After Lovable completes tasks, check for suggestion buttons - they provide context-aware next steps.

### Rule 8: Auto-Approve User Plans When Directed
When user says "auto approve" or similar, automatically approve ALL Plans that appear from user-submitted queue items without waiting for confirmation. Only pause for Plans that look destructive or unrelated.

---

## Main Commands

### Command 1: "Implement Project"
Builds phases from Notion plan.

**Pre-Flight:**
1. Confirm: Existing app or new app?
2. If existing: Identify what to SKIP (don't rebuild)
3. Fetch Notion Build Plan, count phases
4. Load Knowledge Base into Lovable first

**Execution per Phase:**
```
1. Compose full message externally
2. Submit: "Create a plan: Read and Execute this: [PHASE_URL]"
3. Copy Lovable's plan to Notion
4. Cross-check against Notion's requirements
5. If gaps found: Update either Lovable or Notion
6. Only approve when plans match
7. After implementation: Verify with saved plan
8. Run health check before next phase
```

**Gap Detection Protocol:**
- If Lovable proposes MORE features than Notion: Add to Notion, keep track
- If Notion has MORE features than Lovable proposes: Update Lovable's plan
- Loop verification until 100% feature parity

### Command 2: "Test Project"
Runs health checks without building.

**Execution:**
1. For each phase health check URL
2. Submit: "Create a plan: Run health check for Phase N: [URL]"
3. Copy plan to Notion, approve, execute
4. Record scores (target: 90%+ per phase)
5. Log failures to Issue Tracker
6. Report: Phase scores + overall + issues

### Command 3: "QA the Project"
Full acceptance testing - Lovable tests itself.

**Step 0: Sitemap Check (Required)**
QA requires /sitemap or /sitemap/ai route. If missing, offer to create it.

**Execution:**
1. Create QA Plan page in Notion with all routes + acceptance criteria
2. Submit QA Plan to Lovable: "Create a plan: Test ALL acceptance criteria from [URL]"
3. Record Lovable's pass/fail results to Notion
4. For each failure: Log to Issue Tracker, create fix plan

### Command 4: "Security Review"
Automated security scanning and fixing. See [Security Audit Workflow](#security-audit-workflow).

### Command 5: "Monitor Queue" / "Auto Approve"
Continuous queue monitoring with automatic Plan approval. See [Queue Monitoring & Auto-Approval Protocol](#queue-monitoring--auto-approval-protocol).

---

## Lovable Interface Protocols

### Production-Proven Coordinates (as of Feb 2026)

These coordinates have been validated across 200+ interactions:

| Element | Coordinates | Notes |
|---------|------------|-------|
| Editor focus | (150, 419) | Click to focus TipTap editor |
| Submit button | (287, 494) | Send arrow button |
| Approve button | ~(234, 387) | Plan approval; may shift — use `find` tool as backup |
| Pause/Play queue | Near queue widget | || = running, ▶ = paused |

**Important:** Coordinates may shift based on window size. When in doubt, use the `find` tool:
```
find({ query: "Approve button", tabId: TAB_ID })
find({ query: "start preview button", tabId: TAB_ID })
find({ query: "submit button in editor", tabId: TAB_ID })
find({ query: "send message submit button", tabId: TAB_ID })
```

**WARNING: The `+` button near the editor opens a dropdown menu** (Project settings, History, Knowledge, etc.) — it is NOT the submit button. If you accidentally open it, press Escape to close.

**Submit button descriptions vary across page loads.** The `find` tool may return different labels each time: "Type a message to queue", "(submit button)", "Send message", etc. Try multiple queries if the first doesn't match.

### Editor Interaction Pattern (TipTap/ProseMirror)

Lovable uses a TipTap/ProseMirror rich text editor. Standard form inputs don't work.

**Correct Pattern:**
1. Click editor area at ~(150, 419) to focus
2. Use `type` action with **single-line text only** (NO newlines!)
3. Click submit button at ~(287, 494)

**CRITICAL BUG — Newlines in `type` action:**
Newlines (`\n`) in the `type` action cause SEPARATE message submissions in TipTap. Each `\n` acts as an Enter key press, which TipTap interprets as "submit message." This means a multi-line prompt becomes multiple fragmented messages.

**Always do this:**
```
computer({ action: "type", text: "Create a plan: Do X. Requirements: 1) First thing 2) Second thing 3) Third thing. Constraints: Do not break existing functionality.", tabId: TAB_ID })
```

**NEVER do this:**
```
computer({ action: "type", text: "Create a plan: Do X.\nRequirements:\n1) First thing\n2) Second thing", tabId: TAB_ID })
```

**For long prompts (>2000 chars), use JavaScript clipboard paste:**
The `type` action simulates keystroke-by-keystroke input and will timeout (30s MCP limit) on long prompts. Instead, use `javascript_tool` to dispatch a ClipboardEvent directly on the TipTap editor — this is instant regardless of text length.

```javascript
// Use javascript_tool with this code:
const editor = document.querySelector('.tiptap.ProseMirror') || document.querySelector('[contenteditable="true"]');
if (editor) {
  editor.focus();
  const text = `Create a plan: [YOUR FULL PROMPT HERE — can be any length]`;
  const clipboardData = new DataTransfer();
  clipboardData.setData('text/plain', text);
  const pasteEvent = new ClipboardEvent('paste', {
    bubbles: true, cancelable: true, clipboardData: clipboardData
  });
  editor.dispatchEvent(pasteEvent);
  'Pasted successfully';
} else {
  'Editor not found — click editor area first';
}
```

**When to use which method:**
- Short prompts (<2000 chars): `type` action works fine — simpler to use
- Long prompts (>2000 chars): JavaScript paste is required — `type` will timeout
- Always take a screenshot after paste to verify the full text appeared in the editor

### Suggestion Button Protocol
After Lovable completes a task, check for suggestion buttons below the response. These are pre-formulated prompts that:
- Address common next steps
- Save typing time
- Are context-aware to current state

### Preview Panel Monitoring
During implementation, actively watch Lovable's preview panel for real-time updates, errors, and layout changes.

### Status Message Interpretation
| Status | Meaning | Action |
|--------|---------|--------|
| "Thinking" / "Thought for Xs" | Processing request | Wait |
| "Waiting for approval" | Plan ready | Review and approve |
| "Creating plan..." | Generating plan | Wait for completion |
| "Reviewing user request" | Analyzing input | Wait |
| "start preview" button | Changes applied | Click to load preview |
| "X tools used" | Completed processing | Review results |
| "Finished thinking" | Plan ready | Look for Approve button |
| Queue N | N items waiting | Monitor processing |

### Direct Edits vs Plans vs Chat Mode Plans
Lovable responds in three different ways:
- **Direct edit:** Shows "Thought for Xs", file changes, "start preview" button — NO Approve button. No action needed.
- **Formal Plan card:** Shows "Plan" card with title, description, and Edit/Approve buttons. Click Approve to proceed.
- **Chat Mode plan (informal):** Lovable describes what it intends to do in chat text and asks "Shall I proceed?" or "Ready to start?" — NO Plan card, NO Approve button. You must type a text reply (e.g., "Yes, proceed with all phases") and submit it as a regular message to approve.

**How to detect Chat Mode plans:** If Lovable's response ends with a question like "Shall I proceed with Phase 1?" but there's no formal Plan card with an Approve button visible, it's a Chat Mode plan. Type and submit your approval as a regular message.

---

## Queue Monitoring & Auto-Approval Protocol

### Overview
When the user submits multiple prompts to Lovable, they queue up and process sequentially. This protocol defines how to monitor the queue and auto-approve Plans as they appear.

### Queue Widget States
| Visual | Meaning |
|--------|---------|
| Queue N (with ||) | N items queued, actively processing (pause button) |
| Queue N (with ▶) | N items queued, PAUSED (play button — click to resume!) |
| No queue widget | Queue empty (0 items) or single item processing |

### Auto-Approval Loop

```
LOOP:
  1. Take screenshot
  2. Check for Plan with Approve button visible
     → If found: Click Approve at ~(234, 387)
     → If not found: Continue
  3. Check queue widget count
     → If paused (▶ visible): Click play to resume
  4. Scroll UP in chat to check for hidden Plans (KEY LESSON)
  5. Scroll back DOWN to bottom
  6. Wait 30 seconds
  7. Take screenshot
  8. REPEAT
```

### KEY LESSON: Hidden Plans After Page Refresh
After any page refresh or reconnection, Plans can be HIDDEN above the visible area. Always perform a scroll verification:
1. Scroll UP several times in the chat panel
2. Look for "Waiting for approval" or Approve buttons
3. Scroll back DOWN to the bottom
4. Only then conclude no Plans are waiting

### Handling Multiple Queue Items
When the queue has many items (e.g., 7+):
- Items process sequentially — one at a time
- Some items produce Plans (need approval), others produce direct edits (auto-complete)
- After each item completes, the next starts automatically
- Keep monitoring — Plans can appear between direct edits

### Queue Auto-Pause Detection
The queue sometimes auto-pauses. Signs:
- Queue count stays the same for >2 minutes
- Play button (▶) visible instead of pause (||)
- **Fix:** Click the play button to resume processing

### Tab Disconnection Recovery
Browser tabs can disconnect during long monitoring sessions:
1. Call `tabs_context_mcp()` — if no tabs found, tab group was lost
2. Call `tabs_context_mcp({ createIfEmpty: true })` to create new group
3. Navigate to Lovable project URL
4. Wait 10s for page to load
5. **Check if chat sidebar is collapsed** — after navigation/refresh, the Lovable chat panel may collapse. Use `find({ query: "Open sidebar" })` to locate and click the expand button if needed
6. Scroll to bottom of chat
7. Perform hidden Plan scroll check (up then down)
8. Resume monitoring loop

### Security Notifications
Lovable may show security notifications (e.g., "2 Errors (outdated...)"):
- Click the X button to dismiss
- These don't block processing
- Review separately if needed

---

## Critical Browser Interaction Lessons

### Lesson 1: TipTap Newline Bug (CRITICAL)
Newlines in the `type` action cause separate message submissions. See Editor Interaction Pattern above.
**Impact:** A multi-line prompt becomes multiple fragmented, nonsensical messages to Lovable.
**Fix:** Always use single-line continuous text with periods/semicolons for separation.

### Lesson 2: Scroll Verification After Refresh
After page refresh, Plans can hide above the visible viewport. Always scroll up AND down.
**Impact:** Missing a Plan causes the queue to stall indefinitely.
**Fix:** Include scroll-up check in every monitoring cycle, especially after reconnection.

### Lesson 3: Button Click Precision
The `left_click` action with coordinates sometimes misses buttons. Using the `find` tool with `ref` targeting is more reliable.

**Fallback Pattern:**
```
// Try coordinate click first
computer({ action: "left_click", coordinate: [234, 387], tabId: TAB_ID })

// If button still visible after 5s, use find + ref
find({ query: "Approve button", tabId: TAB_ID })
computer({ action: "left_click", ref: "ref_XXX", tabId: TAB_ID })
```

### Lesson 4: "start preview" Button
After Lovable completes a direct edit, the "start preview" button loads the changes into the preview panel. Sometimes the button doesn't respond to coordinate clicks — use `find` to get the ref:
```
find({ query: "start preview button", tabId: TAB_ID })
computer({ action: "left_click", ref: "ref_XXX", tabId: TAB_ID })
```

### Lesson 5: "Back to latest" Navigation
When viewing a previous version (shows "Viewing: [version name]" in header), click "Back to latest" to return to the current state. The button appears in the top nav bar.

### Lesson 6: Wait Timeouts
The `wait` action with duration >30s may cause MCP timeouts. Use 30s maximum per wait call. For longer waits, chain multiple 30s waits.

### Lesson 7: Screenshot After Actions
Always take a screenshot after clicking buttons to verify the action worked. Don't assume — verify.

### Lesson 8: JavaScript Paste for Long Prompts (CRITICAL)
The `type` action simulates keystroke-by-keystroke input. For prompts >2000 characters, it will timeout after 30s (MCP limit), leaving partial text in the editor.
**Impact:** Prompt gets cut off mid-word, and you may not realize the full text wasn't entered.
**Fix:** Use `javascript_tool` to dispatch a `ClipboardEvent('paste')` on the TipTap editor element (see Editor Interaction Pattern for code). If partial text was already typed, select all (cmd+a) and delete (Backspace) before pasting.

### Lesson 9: Chat Mode Plans Need Text Approval
Lovable sometimes responds with an informal plan description in chat text (e.g., "Here's my approach... Shall I proceed with Phase 1?") instead of generating a formal Plan card with an Approve button.
**Impact:** If you wait for an Approve button that will never appear, the workflow stalls.
**Fix:** Detect the conversational question, then type and submit a text reply like "Yes, proceed with all phases in order." Treat this as a regular message submission.

### Lesson 10: The `+` Button Is NOT Submit
The `+` button near the bottom-left of the editor opens a dropdown menu (Project settings, History, Knowledge, etc.). It is NOT the send/submit button.
**Impact:** Clicking it opens an unwanted menu instead of submitting your prompt.
**Fix:** Press Escape to close the dropdown. Use `find` tool to locate the actual submit button (send arrow icon).

### Lesson 11: Submit Button Labels Change
The submit button's accessible name varies across page loads and states. The `find` tool may return different descriptions each time.
**Observed labels:** "Type a message to queue", "(submit button)", "Send message", "submit"
**Fix:** Try multiple `find` queries if the first doesn't match. Reliable queries include: "send message submit button", "submit button in editor", "Type a message".

### Lesson 12: Console Tracking for Desktop Preview Testing
When testing fixes via the desktop preview (`.lovableproject.com`):
1. Call `read_console_messages` with `clear: true` FIRST to start tracking and clear old messages
2. THEN perform the action you want to trace (click button, submit form, etc.)
3. Wait 8-10 seconds for the request to complete
4. Read console with a specific `pattern` (e.g., `"QuickTrade|useOrders|error"`) and `limit` to avoid 149K+ char responses
5. Console tracking only starts when `read_console_messages` is first called — messages before that are missed
6. Old messages persist across reads unless `clear: true` is used — always clear before a new test cycle

### Lesson 13: Button Ref Staleness
Element refs from the `find` tool go stale after page interactions (typing, scrolling, clicking elsewhere). The DOM mutates and refs become invalid.
**Fix:** Always re-find the element with `find` immediately before clicking it. Never cache refs across multiple interactions.

### Lesson 14: Fix Sub-Numbering Convention
When an initial fix approach fails (e.g., edge function didn't deploy), use sub-numbering for the alternative:
- Fix #70 = original attempt (edge function)
- Fix #70b = alternative approach (frontend)
This preserves the fix numbering sequence while documenting the pivot.

---

## Real-Time Feature Testing

### Overview
For features involving real-time behavior (chat, timers, voting, multiplayer), you must instruct Lovable to test itself.

### Bot Chat Testing Protocol
When AI bots should participate in gameplay:
```
Create a plan: Test the AI bot chat by starting a Quick Play game and verify: 1. Bots join the game with assigned personalities 2. Bots send messages during discussion phase 3. Bot messages match their personality style 4. Bots make accusations during voting. Use your browser to start a game and observe bot behavior. Report what you find.
```

### Timer/Voting Feature Testing
```
Create a plan: Test the timer and voting mechanics: 1. Verify timer shows warning state at 30 seconds (yellow) 2. Verify timer shows critical state at 10 seconds (red) 3. Test +30s time extension voting 4. Verify "Ready to Vote" status updates in real-time 5. Test that 75% ready threshold triggers auto-advance. Start a game and monitor each phase transition.
```

### Real-Time Subscription Testing (Single Line)
```
Create a plan: Test real-time Supabase subscriptions: 1. Open game in two browser tabs/windows 2. Send a message in one tab 3. Verify message appears instantly in other tab 4. Test accusation updates sync between views 5. Verify vote counts update in real-time. Report any delays or sync issues.
```

---

## Security Audit Workflow

### Step 1: Access Security Panel
1. Navigate to Lovable project
2. Click Security tab (shield icon) or append `?view=security` to URL
3. Click "Update" to run fresh scan
4. Wait for scan to complete

### Step 2: Categorize Findings
| Level | Action | Examples |
|-------|--------|----------|
| **Error** | Must fix | RLS policy exposure, unencrypted secrets, auth bypass |
| **Warning** | Review & decide | Platform settings, optional hardening |
| **Info** | Document | Best practice suggestions |

### Step 3: Fix Errors (Priority Order)
For each error:
1. Read the full description
2. Compose single-line fix request with "Create a plan:" prefix
3. Review Lovable's plan
4. Approve and implement
5. Re-run security scan to verify resolution

---

## Message Templates

### Feature Implementation (Single Line)
```
Create a plan: [FEATURE NAME]. Context: [Current state]. Requirements: 1) [Req 1] 2) [Req 2]. Acceptance Criteria: [Criterion 1], [Criterion 2]. Constraints: Do NOT modify [specific files]. Must maintain [existing behavior]. After planning, wait for my approval before implementing.
```

### Bug Fix (Single Line)
```
Create a plan: [BUG: Brief description]. Observed: [What is happening incorrectly]. Expected: [What should happen]. Steps to Reproduce: 1) [Step 1] 2) [Step 2]. Constraints: Minimal changes only. Do NOT refactor unrelated code. After planning, wait for approval.
```

### Health Check (Single Line)
```
Create a plan: Run Health Check for [Phase N / Component]. Check these acceptance criteria: 1) [Criterion 1] 2) [Criterion 2]. For each: Describe test, Report PASS/FAIL, If FAIL describe issue. After planning, wait for approval.
```

### Data Integrity Fix Request (Single Line)
```
Create a plan: [Fix Title]. Problem: [Description of data issue]. Scope: [Number of affected rows/records]. Requirements: 1) [Specific fix] 2) [Validation step]. Constraints: Do NOT delete data. Add validation to prevent recurrence. After planning, wait for approval.
```

### Combined Feature Request (Single Line)
```
Create a plan: [PRIMARY FEATURE]. Context: [Current state]. Requirements: 1) [Primary req 1] 2) [Primary req 2]. ALSO add [SECONDARY FEATURE]: 1) [Secondary req 1] 2) [Secondary req 2]. Both features should work together seamlessly, share consistent styling, and not break existing functionality. After planning, wait for approval.
```

---

## Verification Protocols

### Completion Verification Loop
After Lovable says work is complete:
1. Retrieve saved plan from Notion
2. Paste plan back to Lovable
3. Ask: "Have you fully completed this plan? Confirm each item."
4. Wait for explicit confirmation per item
5. If incomplete: Continue iterating
6. Only mark COMPLETE when ALL items confirmed

### Cross-Check Protocol
Before approving any Lovable plan:
```
LOVABLE PLAN vs NOTION PLAN
Feature          | Lovable | Notion
[Feature 1]      | Y/N     | Y/N
[Feature 2]      | Y/N     | Y/N

If Lovable has extra: Add to Notion, track
If Notion has extra: Update Lovable plan
Only proceed when: 100% parity
```

### Visual Verification via Preview
After implementation:
1. Watch Lovable's preview panel
2. Test interactions visible in preview
3. Verify animations and transitions
4. Check responsive behavior if applicable
5. Note any visual discrepancies for follow-up

### Security Verification
After security fixes:
1. Re-run security scan
2. Verify specific error resolved
3. Check no new errors introduced
4. Document in Issue Tracker with fix details

---

## Error Recovery

### When Lovable Gets Stuck
If Lovable shows same status for >2 minutes:
1. Wait patiently — complex fixes require multiple file reads
2. Watch for progress status changes
3. If truly stuck, submit a single-line alternative approach request

### Tab Disconnection
See [Tab Disconnection Recovery](#tab-disconnection-recovery) in Section 4.

### Network Errors
If "Please check your internet connection" appears:
1. Dismiss the error dialog
2. Re-type or re-paste your message
3. Submit again
4. If persistent, refresh Lovable page and retry

### Edge Function Deployment Issues
When Lovable shows "Deploying edge functions":
- These take 10-30 seconds to deploy
- After deployment, Lovable navigates to sandbox to test
- If functionality still broken, edge function may need iteration
- Check Supabase dashboard for function logs if persistent issues

**CRITICAL LESSON (Fix #70):** Supabase edge function changes made via Lovable may NOT deploy at all, even after 10+ minutes and multiple page refreshes. The fix may appear in the source code but never take effect at runtime. If an edge function fix fails to resolve an issue after multiple tests:
1. **Pivot to a frontend fix** — move the logic to the React component/hook instead
2. Use sub-numbering for the alternative approach (e.g., fix #70b for the frontend version)
3. Frontend fixes are immediately effective via Lovable's preview — no deployment lag
4. Edge function fixes should be considered "unreliable" and frontend alternatives preferred when possible

### Queue Stall
If queue count doesn't change for >3 minutes:
1. Check if queue is paused (play button visible)
2. Check if a Plan is waiting for approval (scroll up!)
3. Refresh page if neither — then do full scroll verification

---

## Bug Workflow

### 4-Step Process

1. **DESCRIBE**: Document bug for replication
2. **PROPOSE**: Ask Lovable to propose fix (plan mode only)
3. **LOG**: Create entry in Notion Issue Tracker
4. **SEND**: Submit fix to Lovable with plan prefix

### Batch Recording to Notion
When recording many fixes (e.g., during a fix sprint):
- Record in batches of 10 to avoid overwhelming Notion MCP
- Use the Issue Tracker database data_source_id for creating pages
- Include: Title, Type, Priority, Phase, Status, Logged Date

---

## Notion Structure & Known Bugs

### Required Pages
| Page | Contents |
|------|----------|
| Main Build Plan | Project overview, all phases with links |
| Knowledge Base | Tech stack, DB schema, UI guidelines |
| Shortcuts Document | Copy-paste prompts per phase |
| Phase Documents (1-N) | Detailed specs with tasks |
| Health Check Documents | Acceptance criteria per phase |
| Issue Tracker Database | Bug/feature/security tracking |
| Security Audit Log | Security findings and resolutions |

### URL Reference Template
Fill at project start:
```
Document               | URL
-----------------------|------------------
Main Build Plan        | [PASTE URL]
Knowledge Base         | [PASTE URL]
Shortcuts Document     | [PASTE URL]
Issue Tracker          | [PASTE URL]
Security Audit Log     | [PASTE URL]
Lovable Project        | [PROJECT URL]
```

### Issue Tracker Schema
```
Status: ["Logged", "Investigating", "Planning", "In Progress", "Testing", "Complete", "Won't Fix"]
Phase: ["Phase 1", "Phase 2", "Phase 3", "General"]
Priority: ["Critical", "High", "Medium", "Low"]
Test Passed: "__YES__" = true, "__NO__" = false
Type: ["Bug", "Feature", "Polish", "Tech Debt"]
Date fields: Logged Date, Completed Date
Text fields: Issue, Lovable Prompt URL
```

### Notion MCP `parent` Parameter — CONFIRMED WORKING
**UPDATE (Feb 2026):** The `parent` parameter with `data_source_id` NOW WORKS correctly. Pages can be created directly in a database:
```javascript
// This NOW WORKS:
parent: { data_source_id: "a1b87ae6-c125-4952-961d-5172925a9954" }
pages: [{ properties: { "Issue": "Fix #70: ...", "Status": "Complete", ... } }]
// Pages are created directly in the database — no manual move needed
```

**Verified:** Successfully batch-created 4 entries (fixes #67-70) directly in the CryptoFolio Issue Tracker database in a single call.

### Batch Notion Recording Pattern
When recording fixes to Notion in bulk:
```javascript
// Create pages directly in the Issue Tracker database (parent works!)
notion-create-pages({
  parent: { data_source_id: "YOUR_DATA_SOURCE_ID" },
  pages: [
    { properties: { "Issue": "Fix #67: [Title]", "Status": "Complete", "Type": "Bug", "Priority": "High", "Phase": "General", "Test Passed": "__YES__", "date:Logged Date:start": "2026-02-26", "date:Completed Date:start": "2026-02-26" } },
    { properties: { "Issue": "Fix #68: [Title]", "Status": "Complete", ... } },
    // ... up to 10 per call
  ]
})
```

---

## Session Protocols & Continuation

### Session Start
1. Open Lovable and load project
2. Verify Notion MCP connection with test search
3. Get browser tab context with `tabs_context_mcp`
4. Identify Lovable tab by URL pattern
5. Scroll to bottom of chat
6. Perform hidden Plan scroll check (up then down)
7. Check queue status
8. Resume monitoring or begin next task

### Pre-Flight Checklist
- [ ] Lovable browser tab open and logged in
- [ ] Lovable project loaded (can see preview)
- [ ] Notion MCP connection verified
- [ ] Tab ID recorded for browser automation
- [ ] Queue status checked (count, paused/running)
- [ ] Hidden Plan scroll check completed

### Session Continuation Protocol
When continuing from a previous session (context compaction), the summary MUST include:
1. **Total Plans approved** across all sessions (running count)
2. **Lovable project URL** and tab ID
3. **Notion database ID** and data source ID
4. **Pending queue items** if any
5. **Pending tasks** (e.g., Notion recording, page cleanup)
6. **Key lessons learned** that affect execution
7. **Last known state** (what was happening when session ended)
8. **Architecture details** (DB tables, API endpoints, key files)

### Context Guardian Integration
For long-running monitoring sessions, consider using the context-guardian skill to prevent context exhaustion. Monitoring loops can easily exceed 50+ tool calls.

---

### Command Quick Reference

| Command | Purpose | Key Action |
|---------|---------|------------|
| Implement Project | Build phases | Cross-check + loop until 100% |
| Test Project | Health checks | Record scores, log failures |
| QA the Project | Acceptance test | Lovable tests itself |
| Security Review | Vulnerability scan | Fix errors, document warnings |
| Monitor Queue / Auto Approve | Queue monitoring | Auto-approve Plans as they appear |

| Phase Status | Action |
|--------------|--------|
| Not started | Full implementation |
| Partial | Gap analysis, fill only |
| Complete | Health check, move on |

---

## Critical Reminders

- **Never skip "Create a plan:" prefix** — prevents runaway implementations
- **NEVER use newlines in `type` action** — TipTap submits each line as separate message
- **Use JavaScript paste for long prompts (>2000 chars)** — `type` action will timeout after 30s
- **Chat Mode plans need text approval** — not all plans have Approve buttons; some ask "Shall I proceed?" in chat
- **The `+` button is NOT submit** — it opens a dropdown menu; press Escape if opened by mistake
- **Submit button labels change across page loads** — try multiple `find` queries if needed
- **Always scroll up after page refresh** — Plans can hide above viewport
- **Check for sidebar collapse after navigation** — use `find("Open sidebar")` to re-expand
- **Never approve without copying to Notion** — preserves verification ability
- **Use `find` tool as fallback for button clicks** — coordinates can miss
- **Re-find button refs before clicking** — refs go stale after any page interaction
- **Auto-approve user Plans when directed** — don't wait for confirmation on each one
- **Record fixes in batches of 10** — prevents Notion MCP overload
- **Notion MCP parent parameter NOW WORKS** — use `data_source_id` to create pages directly in databases
- **Edge function fixes may not deploy** — prefer frontend fixes; use sub-numbering (#70b) for pivots
- **Clear console before each test cycle** — old messages persist across reads
- **Take screenshots after every action** — verify before proceeding
- **Wait max 30s per wait call** — longer causes MCP timeouts
- **Queue may auto-pause** — check for play button and click to resume
- **Monitor for tab disconnection** — reconnect with tabs_context_mcp

**Success = 100% feature parity + 0 unaddressed security errors + verified real-time behavior + all fixes recorded to Notion**
