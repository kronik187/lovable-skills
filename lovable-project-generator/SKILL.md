---
name: lovable-project-generator
description: |
  Use this skill when users want to create a NEW Lovable project from scratch. Triggers: 
  "new app", "build an app", "project intake", "generate project plan", "create project structure",
  "set up Notion for Lovable", "start a new project", or describing an app idea they want to build.
  This skill provides a comprehensive intake questionnaire and generates a complete Notion project structure.
---

# Lovable Project Generator

## Overview

This skill guides users through a structured intake process to define their app idea, then generates a complete Notion project structure with phases, health checks, and issue tracking.

## When to Use

- User describes a new app they want to build
- User says "I want to build an app with Lovable"
- User needs help planning a project before implementation
- User wants to set up a proper Notion project structure

---

## The 9-Stage Intake System

Guide users through these stages. They can stop at any checkpoint - more stages = better output.

### Stage 0: Starting Point (Required)
Ask: "What are we building?"
- 🆕 **New Idea** - User has a concept, help shape it
- 📋 **Copy/Improve** - Clone or improve an existing app
- 🔧 **Extend Existing** - Adding features to existing Lovable project
- 🔄 **Rebuild** - Moving app from another tech to Lovable

### Stage 1: Core Definition (Required - 5 min)

**If NEW IDEA:**
- One sentence: What does it do?
- Who uses it: Individual / Small team / Business / Enterprise?
- The trigger: Why now? What problem hit you?

**If COPY/IMPROVE:**
- Target app URL(s) to analyze
- What you love about it
- What's broken/missing
- Your unfair advantage

**If EXTEND EXISTING:**
- Lovable project URL or name
- Current tech stack (Supabase? Other?)
- What exists today (bullet points)
- What you're adding

**If REBUILD:**
- Current app URL or screenshots
- Current tech
- What to keep vs change
- Why rebuilding

### Stage 2: Scope & Constraints (Required - 5 min)

**Data scale:**
- Small (<1K records)
- Medium (1K-50K)
- Large (50K-500K)
- Massive (500K+)

**Users:**
- Just me
- Small team (<10)
- Organization (10-100)
- Public/SaaS (unlimited)

**Timeline pressure:**
- Exploring/learning
- Side project (no deadline)
- Real deadline: [date]
- Already behind

**Budget reality:**
- Free tier only
- Can pay for Lovable Pro
- Can pay for APIs/services
- Business budget available

**Non-negotiables:** (things that MUST work or it's worthless)
1. ___
2. ___
3. ___

### Stage 3: Feature Brain Dump (Recommended - 10 min)

Ask user to list everything without filtering:
- **Must have (Day 1):**
- **Should have (Month 1):**
- **Nice to have (Someday):**
- **Explicitly NOT doing:**

---

## 🛑 CHECKPOINT A

With Stages 0-3, you can provide:
- ✅ Basic phase structure (3 phases)
- ✅ Core database schema
- ✅ Main feature prompts
- ⚠️ Generic UI (no custom styling)
- ⚠️ Standard patterns (may not fit their flow)

Ask: "Good enough? Or continue to Stage 4 for better results?"

---

### Stage 4: User Experience (Recommended - 10 min)

**User types/roles:**
| Role | Can do | Cannot do |
|------|--------|-----------|

**Critical user flows:** (step-by-step)
- Flow 1: [name] → Steps 1-5 → Success/Failure looks like...
- Flow 2: ...

**States to handle:**
- Empty state (new user, no data)
- Error state (something breaks)
- Loading state (slow connection)
- Offline state (if applicable)

### Stage 5: Visual & Brand (Optional - 5 min)

**Existing brand?**
- No - suggest something
- Yes - provide logo/colors/guidelines

**Vibe:**
- Minimal/clean
- Playful/fun
- Professional/corporate
- Bold/disruptive
- Dark/techy
- Warm/friendly

**Reference apps:** (UI you admire)
**Anti-references:** (UI styles you hate)

### Stage 6: Technical Requirements (If applicable - 10 min)

**Authentication:**
- None needed
- Email/password
- Social login (which?)
- SSO/Enterprise
- Existing auth system

**Integrations needed:**
| Service | Purpose | Have credentials? |

**External data sources:**
- None / API / Database sync / Files / Scraping

---

## 🛑 CHECKPOINT B

With Stages 0-6, you can provide:
- ✅ Everything from Checkpoint A
- ✅ Custom user flows
- ✅ Role-based access design
- ✅ Styled UI mockup descriptions
- ✅ Integration architecture

Ask: "Good enough? Or continue for comprehensive plan?"

---

### Stage 7: Business & Competition (Optional - 15 min)

**Monetization model:**
- Free forever / Freemium / Subscription / One-time / Transaction fees / Ads

**Competitors:** (provide URLs to analyze)
| Competitor | URL | What they charge | Their weakness |

**Your differentiation:** Price / Feature / Audience / Experience

**Success metrics:**
- In 30 days, success = ___
- In 90 days, success = ___
- In 1 year, success = ___

### Stage 8: Edge Cases & Complexity (Optional - 15 min)

**Data relationships:**
- What are your main "things"? (Users, Products, Orders, etc.)
- How do they relate? (A ___ has many ___, belongs to ___)

**Tricky scenarios:**
- What if user does X but Y hasn't happened yet?
- What if two users try to ___ at same time?
- What happens to ___ when ___ is deleted?

**Compliance/Security:**
- None special / Financial (PCI) / Health (HIPAA) / European (GDPR) / Audit trail

### Stage 9: Assets & Context (Optional)

Ask user to upload/share:
- Spreadsheet of current data (for schema design)
- Screenshots of competitors (annotated)
- Existing database schema (if rebuilding)
- Wireframes/sketches
- Previous failed attempts

---

## Output: 11-Page Notion Structure

After intake, generate these Notion pages:

### 1. Main Project Page
- Project overview and description
- Tech stack summary
- Monetization breakdown
- Phase summary table with status
- Quick links to ALL subpages
- "How to Use" instructions for Lovable
- Global Lovable instructions
- Build log table

### 2. Knowledge Base Page
- Project identity (name, tagline, colors, style)
- Full tech stack details
- User roles and permissions
- Complete database schema (SQL ready)
- All features by phase
- UI/UX guidelines with color tokens
- API integrations details
- Security rules and RLS policies
- File structure
- Testing checklist template

### 3. Shortcuts Document
Pre-written commands for every phase:
```
Read and Execute this: [PHASE 1 URL]
Run health check: [HEALTH CHECK 1 URL]
Read and memorize this Knowledge Base: [KB URL]
```

Issue tracking shortcuts:
```
bug: [description]
feature: [description]
polish: [description]
```

### 4-6. Phase Documents (1-3)

**Phase 1: MVP Foundation**
- Authentication system
- Core data models (database schema)
- Main UI screens
- Primary feature
- Basic navigation
- Mobile responsive design

**Phase 2: Monetization + Key Features**
- Payment/subscription system
- Secondary features (2-3)
- User engagement features
- Analytics tracking
- Admin dashboard basics

**Phase 3: Growth + Polish**
- Advanced features
- Gamification (if applicable)
- Content moderation tools
- Performance optimization
- Final deployment checklist

Each phase page includes:
- Phase status table
- LOVABLE MASTER INSTRUCTION block
- 5-8 tasks with acceptance criteria
- "Lovable Completed" section (empty for Lovable to fill)
- Build log table

### 7-9. Health Check Documents (per phase)

12 verification tasks each:
1. Review Original Plan
2. Feature Completion Audit
3. Core Functionality Tests
4. Edge Cases & Error Handling
5. User Flow Verification
6. Code Quality Review
7. Requirements Verification
8. Document Limitations
9. Implementation Report
10. Next Phase Proposal
11. Issue Tracker Review
12. Format Final Proposal

### 10. Deep Health Check Template (Clonable)
Generic version for ad-hoc checks, pre-launch audits, post-bug-fix verification.

### 11. Issue Tracker Database
Properties:
- Name (Title)
- Type (Bug/Feature/Polish)
- Status (Logged/Investigating/Planning/In Progress/Testing/Complete/Won't Fix)
- Priority (Critical/High/Medium/Low)
- Phase
- Logged Date
- Completed Date
- Test Passed (Checkbox)
- Page Modified
- Lovable Prompt URL

---

## Lovable Master Instructions (Include in Phase Pages)

```
YOU ARE READING A NOTION PAGE. FOLLOW THESE INSTRUCTIONS EXACTLY:

1. READ this entire page before taking any action
2. VERIFY Knowledge Base is loaded in your project settings
3. EXECUTE tasks in order (X.1 → X.2 → X.3 → etc.)
4. For EACH task:
   a. Use Chat/Plan Mode first - output your plan
   b. Wait for approval OR proceed if plan is clear
   c. Implement with minimal diff
   d. Test the feature
   e. UPDATE this Notion page:
      - Check the task checkbox ✅
      - Fill in the "Lovable Completed" section
      - Update the PHASE STATUS table
   f. If issues found, LOG to Issue Tracker
5. If a task FAILS:
   a. Mark status as ⚠️
   b. Document error in "Blockers" section
   c. Log to Issue Tracker as bug:
   d. Attempt to fix OR ask user for guidance
6. After ALL tasks complete:
   a. Update Phase Status to ✅ Complete
   b. Write summary in BUILD LOG
   c. Instruct user to run Health Check before next phase

DO NOT SKIP TASKS. DO NOT PROCEED TO NEXT PHASE WITHOUT HEALTH CHECK.
```

---

## Health Check Master Instructions

```
YOU ARE PERFORMING A DEEP HEALTH CHECK. FOLLOW EXACTLY:

1. READ the original phase URL referenced in this document
2. COMPARE what was planned vs. what exists in the codebase
3. EXECUTE each verification task (1-12) IN ORDER
4. UPDATE each checkbox and fill in ALL results tables
5. Be THOROUGH - test edge cases, not just happy paths
6. DOCUMENT everything with specific details
7. LOG any new issues found to Issue Tracker
8. REVIEW Issue Tracker for this phase
9. CALCULATE health score honestly (target: 90%+)
10. MAKE a clear PROCEED/FIX/STOP recommendation
11. If PROCEED: Generate prompt for next phase
12. If FIX: List exactly what must be fixed

DO NOT INFLATE SCORES. DO NOT SKIP VERIFICATION STEPS.
```

---

## Quick Start

When user triggers this skill:

1. **Ask Stage 0:** "What are we building? New idea, copy/improve, extend existing, or rebuild?"
2. **Progress through stages** until checkpoint or user stops
3. **Generate Notion structure** based on intake depth
4. **Provide all URLs** in a mapping table
5. **Render Shortcuts Reference** with populated URLs
