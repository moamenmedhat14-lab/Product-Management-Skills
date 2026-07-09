# Skill: Backlog Gap Analysis

**Skill ID:** backlog-gap-analysis
**Version:** 1.0
**Triggers:** "run gap analysis", "compare backlog with wiki", "backlog gap analysis for [module]",
              "what's missing from the wiki", "find gaps in [module]"

---

## Purpose
Compare the project's main wiki against the Backlog Wiki for a specific module.
Surface every gap, missed requirement, undocumented detail, and open point.
Show all findings first — no changes are made until explicitly approved.
All approved additions go to the main project raw/ folder for later wiki ingestion.

---

## Source Rule
- **Read from:**
  - `wiki/` — main project wiki (your product knowledge base)
  - `Backlog/Backlog-Wiki/` — backlog wiki (Azure work items)
- **Write to:**
  - `raw/` — main project raw folder (only after your explicit approval)
- **Never read from:** `Backlog/Backlog-Raw/` during gap analysis — use the wiki only
- **Never write directly to** `wiki/` — all additions go to `raw/` first for proper ingestion

---

## Step 0 — Setup

Claude asks:

```
Backlog Gap Analysis

Which module do you want to analyze?
(I'll compare the main project wiki against the Backlog Wiki for that module)
```

Wait for answer. Store as `[MODULE_NAME]`.

Then confirm sources before starting:

```
Running gap analysis for: [MODULE_NAME]

Sources I'll read:
  ✓ Main wiki:     wiki/index.md + relevant pages
  ✓ Backlog Wiki:  Backlog/Backlog-Wiki/index.md + [MODULE_NAME] pages

No changes will be made until you explicitly approve each one.
Starting analysis now...
```

---

## Step 1 — Read Both Wikis

### Read Main Wiki
1. Read `wiki/index.md` — get full map of documented knowledge
2. Read all pages under `wiki/features/` and `wiki/user-flows/` relevant to `[MODULE_NAME]`
3. Extract: every documented feature, requirement, flow, decision, and acceptance criterion

### Read Backlog Wiki
1. Read `Backlog/Backlog-Wiki/index.md`
2. Read all pages for `[MODULE_NAME]`: epics, features, backlog-items, bugs-and-enhancements
3. Extract: every work item, requirement, acceptance criterion, and open point

---

## Step 2 — Run the Gap Analysis (4 gap types)

Analyze all 4 gap types before presenting anything. Complete the full analysis first,
then display all findings together in Step 3.

### Gap Type 1 — Missing Features / Epics
Work items in Backlog Wiki that have NO corresponding entry in the main wiki.
These are features or epics that exist in Azure but are completely undocumented
in the product knowledge base.

Per finding:
```
[GAP-001] Missing: [Feature/Epic Title]
- Azure ID: [ID] | Type: [Epic/Feature/PBI]
- State: [state] | Priority: [priority]
- What it covers: [1-2 sentence summary of the work item]
- Why it matters: [impact if not documented in the wiki]
```

### Gap Type 2 — Incomplete Requirements
Work items that EXIST in both wikis but the main wiki is missing details,
acceptance criteria, edge cases, or specific requirements documented in Azure.

Per finding:
```
[GAP-002] Incomplete: [Feature Title]
- Main wiki page: [[wiki/features/[slug]]]
- Backlog Wiki page: [[Backlog-Wiki/features/[slug]]]
- What's missing from main wiki:
    • [specific missing detail 1]
    • [specific missing detail 2]
    • [missing acceptance criterion]
```

### Gap Type 3 — Open Points & Unanswered Questions
Items in the Backlog Wiki that contain unresolved questions, TBDs, pending decisions,
or items marked as needing clarification in Azure (in descriptions, AC, or tags).

Per finding:
```
[GAP-003] Open Point: [Item Title]
- Azure ID: [ID]
- Open question: [exact question or TBD from the work item]
- Context: [what this blocks or affects]
- Needs: [answer / decision / research / design]
```

### Gap Type 4 — Bugs & Enhancements Not Reflected in Wiki
Bugs or enhancements in Azure that reveal product behavior or requirements
not documented anywhere in the main wiki — meaning the wiki doesn't reflect
the real current state of the product.

Per finding:
```
[GAP-004] Undocumented behavior: [Bug/Enhancement Title]
- Azure ID: [ID] | Type: [Bug/Enhancement] | State: [state]
- What it reveals: [what this tells us about the product that's missing from wiki]
- Should be documented as: [requirement / known issue / flow variation / edge case]
```

---

## Step 3 — Display All Findings (No Action Yet)

Present the complete findings report:

```
─────────────────────────────────────────────
Gap Analysis — [MODULE_NAME]
Compared: [date]
─────────────────────────────────────────────

SUMMARY
  Missing features/epics:              [N]
  Incomplete requirements:             [N]
  Open points:                         [N]
  Undocumented bugs/enhancements:      [N]
  ──────────────────────────────────────────
  Total gaps found:                    [N]

─────────────────────────────────────────────
TYPE 1 — MISSING FEATURES / EPICS ([N])
─────────────────────────────────────────────
[list all GAP-001 findings]

─────────────────────────────────────────────
TYPE 2 — INCOMPLETE REQUIREMENTS ([N])
─────────────────────────────────────────────
[list all GAP-002 findings]

─────────────────────────────────────────────
TYPE 3 — OPEN POINTS ([N])
─────────────────────────────────────────────
[list all GAP-003 findings]

─────────────────────────────────────────────
TYPE 4 — UNDOCUMENTED BUGS / ENHANCEMENTS ([N])
─────────────────────────────────────────────
[list all GAP-004 findings]

─────────────────────────────────────────────

No changes have been made. 
What would you like to do? Options:
  → "Add [GAP-ID]" — add a specific gap to raw/
  → "Add all type 1" — add all gaps of a type
  → "Answer [GAP-ID]: [your answer]" — resolve an open point with your answer
  → "Skip [GAP-ID]" — mark as not needed
  → "Skip all type [N]" — skip an entire gap type
  → "Add all" — add everything to raw/ (I'll confirm each one first)
  → "Show me [GAP-ID]" — see full detail of a specific gap
```

Wait. Do not proceed until you give an instruction.

---

## Step 4 — Process Your Decisions (One at a Time)

For every gap you decide to add, Claude shows exactly what it will write before writing:

```
Adding GAP-001 — [Title]

I will create this file in raw/:
──────────────────────────────────────────
File: raw/[MODULE_NAME]_[slug]_gap.md

---
title: [Title]
type: [feature / requirement / open-point / bug-finding]
source: Azure DevOps [ID]
gap-type: [1/2/3/4]
status: pending-ingestion
added: YYYY-MM-DD
module: [MODULE_NAME]
---

# [Title]

## Summary
[Claude-written summary of what this adds to the knowledge base]

## Requirements / Details
[All relevant content from the Backlog Wiki page]

## Acceptance Criteria
[If exists]

## Notes
[Any context Claude adds about why this matters]
──────────────────────────────────────────
Confirm? Say "yes" to write, "edit [instruction]" to adjust first,
or "skip" to move on.
```

Wait for confirmation on EVERY file. Never write without a "yes".

### For Open Points with Your Answer
When you say "Answer GAP-003-X: [your answer]":

```
Adding GAP-003-X with your answer

File: raw/[MODULE_NAME]_[slug]_resolved-open-point.md

---
title: [Open Point Title] — Resolved
type: resolved-open-point
source: Azure DevOps [ID]
gap-type: 3
status: pending-ingestion
added: YYYY-MM-DD
answer-provided-by: Moamen
---

# [Open Point Title]

## Original Question
[the open point from Azure]

## Answer / Decision
[your answer exactly as you provided it]

## Impact
[what this clarifies or unblocks in the product]
──────────────────────────────────────────
Confirm? Say "yes" or "edit [instruction]"
```

---

## Step 5 — Gap Session Summary

After all decisions are processed:

```
─────────────────────────────────────────────
Gap Analysis Session Complete — [MODULE_NAME]
─────────────────────────────────────────────

Decisions made:
  Added to raw/:    [N] items
  Skipped:          [N] items
  Still pending:    [N] items (gaps you didn't address yet)

Files added to raw/:
  - raw/[filename_1].md
  - raw/[filename_2].md
  - ...

Next step:
  These files are now in raw/ and ready to ingest into the main wiki.
  Say "ingest [filename]" to process each one, or run a full ingest session.

  Pending gaps are saved and can be revisited — say
  "continue gap analysis for [MODULE_NAME]" to pick up where we left off.
─────────────────────────────────────────────
```

---

## Step 6 — Save Gap Session State

After every session, save a gap session file so work is never lost:

`[project-root]/Backlog/gap-sessions/[MODULE_NAME]_[YYYY-MM-DD]_gap-session.md`

```markdown
---
title: Gap Session — [MODULE_NAME]
date: YYYY-MM-DD
module: [MODULE_NAME]
total-gaps: [N]
added: [N]
skipped: [N]
pending: [N]
iteration: 1
---

# Gap Session — [MODULE_NAME] — [YYYY-MM-DD]

## Added to raw/
| GAP-ID | Title | File Created |
|--------|-------|-------------|
| GAP-001 | [title] | raw/[file].md |

## Skipped
| GAP-ID | Title | Reason |
|--------|-------|--------|
| GAP-002 | [title] | [your reason if given] |

## Still Pending (not addressed this session)
| GAP-ID | Title | Type |
|--------|-------|------|
| GAP-003 | [title] | Open Point |
```

---

## Iteration Support

### Continuing a Previous Session
Triggers: "continue gap analysis for [module]", "resume [module] gap session"

1. Read the latest gap session file from `Backlog/gap-sessions/`
2. Show only the pending gaps (not already added or skipped)
3. Continue the decision process from where it stopped

### Re-running After Backlog or Wiki Updates
Triggers: "re-run gap analysis for [module]"

1. Re-read both wikis (main + backlog)
2. Re-run all 4 gap types
3. Cross-check against the previous gap session — mark already-handled gaps
4. Show only NEW gaps since the last session
5. Append to the gap session file with new iteration number:
   `## Iteration 2 — [date]`

### Checking Ingestion Status
Triggers: "what's pending ingestion from [module] gaps?"
→ Read all raw/ files with `status: pending-ingestion` and `module: [MODULE_NAME]`
→ List them with their gap type and date added

---

## Update SKILLS-README.md Entry

```markdown
| backlog-gap-analysis.md | Compare the main project wiki against the Backlog Wiki for a specific module. Surfaces missing features, incomplete requirements, open points, and undocumented bugs. Displays all findings first — no changes made without explicit approval. Approved gaps are written to raw/ for wiki ingestion. Supports session resumption and re-runs after updates. |
```