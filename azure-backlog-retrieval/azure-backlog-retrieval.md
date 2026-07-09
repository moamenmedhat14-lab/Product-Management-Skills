# Skill: Azure Backlog Retrieval

**Skill ID:** azure-backlog-retrieval
**Version:** 2.0
**Triggers:** "retrieve backlog", "pull backlog from azure", "get azure backlog",
              "fetch backlog for [module]", "sync backlog", "ingest [module] backlog into wiki",
              "re-retrieve [module] backlog", "update [module] business knowledge"

---

## Purpose
Retrieve all work items (Epics, Features, Product Backlog Items, Bugs, Enhancements)
for a specific module from Azure DevOps and save them as a structured raw file.
Then synthesize that raw data into a single unified business knowledge document
in the Backlog Wiki — one file per module, written as business narrative with
full detail preserved for every item type.

---

## Folder Structure This Skill Manages

```
[project-root]/
└── Backlog/
    ├── Backlog-Raw/                  ← Azure data lands here. READ ONLY after saved.
    │   └── [Module]_Backlog.md       ← one file per module, replaced on each retrieval
    └── Backlog-Wiki/                 ← LLM Wiki lives here. Claude maintains this.
        ├── index.md                  ← master catalog of all ingested modules
        ├── log.md                    ← append-only operation log
        └── [MODULE_NAME].md          ← one business knowledge document per module
```

Create folders if they don't exist. Never create Backlog/ outside the project root.

---

## Phase 1 — Retrieval from Azure DevOps

### Step 1 — Ask for Module

Claude asks exactly this:

```
Azure Backlog Retrieval

Which module do you want to retrieve from Azure DevOps?
(Type the module/area name exactly as it appears in Azure, or say "all" for everything)
```

Wait for the answer. Do not proceed until confirmed. Store as `[MODULE_NAME]`.

---

### Step 2 — Retrieve from Azure DevOps

Using the connected Azure DevOps MCP, retrieve ALL of the following work item
types that belong to `[MODULE_NAME]`:

- **Epics**
- **Features** (children of Epics where relevant)
- **Product Backlog Items** (PBIs)
- **Bugs**
- **Enhancements**

For each work item retrieve every available field:
- ID
- Title
- Type (Epic / Feature / PBI / Bug / Enhancement)
- State (New / Active / Resolved / Closed / Removed)
- Priority
- Description (full text — never truncate)
- Acceptance Criteria (full text — never truncate)
- Parent ID (to preserve hierarchy)
- Tags
- Assigned To
- Iteration Path
- Area Path

Preserve the full parent-child hierarchy:
```
Epic
└── Feature
    ├── Product Backlog Items
    ├── Bugs
    └── Enhancements
```

---

### Step 3 — Confirm Before Saving

Before saving anything, show a summary:

```
Retrieved from Azure DevOps — [MODULE_NAME]

  Epics:                 [N]
  Features:              [N]
  Product Backlog Items: [N]
  Bugs:                  [N]
  Enhancements:          [N]
  ───────────────────────────
  Total work items:      [N]

Save to Backlog/Backlog-Raw/[MODULE_NAME]_Backlog.md?
Say "save" to confirm or "re-retrieve" to fetch again.
```

Wait for confirmation. Never save without explicit approval.

---

### Step 4 — Save to Backlog-Raw

Only after confirmed, save to:
`[project-root]/Backlog/Backlog-Raw/[MODULE_NAME]_Backlog.md`

```markdown
---
title: [MODULE_NAME] — Azure Backlog
module: [MODULE_NAME]
retrieved: YYYY-MM-DD HH:MM
azure-project: [project name from CLAUDE.md]
total-items: [N]
epics: [N]
features: [N]
pbis: [N]
bugs: [N]
enhancements: [N]
iteration: 1
---

# [MODULE_NAME] — Azure Backlog
**Retrieved:** YYYY-MM-DD
**Azure Project:** [project name]

---

# Epics

## [ID] — [Epic Title]
- **State:** [state]
- **Priority:** [priority]
- **Area Path:** [area path]
- **Iteration Path:** [iteration path]
- **Tags:** [tags]
- **Assigned To:** [name]
- **Description:** [full description — never truncate]

### Features under this Epic

#### [ID] — [Feature Title]
- **State:** [state]
- **Priority:** [priority]
- **Tags:** [tags]
- **Assigned To:** [name]
- **Description:** [full description — never truncate]
- **Acceptance Criteria:** [full text — never truncate]

##### Product Backlog Items

###### [ID] — [PBI Title]
- **State:** [state]
- **Priority:** [priority]
- **Tags:** [tags]
- **Assigned To:** [name]
- **Description:** [full description — never truncate]
- **Acceptance Criteria:** [full text — never truncate]

##### Bugs

###### [ID] — [Bug Title]
- **State:** [state]
- **Priority:** [priority]
- **Tags:** [tags]
- **Assigned To:** [name]
- **Description:** [full description — never truncate]
- **Steps to Reproduce / Fix Criteria:** [full text — never truncate]

##### Enhancements

###### [ID] — [Enhancement Title]
- **State:** [state]
- **Priority:** [priority]
- **Tags:** [tags]
- **Assigned To:** [name]
- **Description:** [full description — never truncate]
- **Acceptance Criteria:** [full text — never truncate]

---

# Current Status Summary

| Type | Total | New | Active | Resolved | Closed |
|------|-------|-----|--------|----------|--------|
| Epics | [N] | [N] | [N] | [N] | [N] |
| Features | [N] | [N] | [N] | [N] | [N] |
| Product Backlog Items | [N] | [N] | [N] | [N] | [N] |
| Bugs | [N] | [N] | [N] | [N] | [N] |
| Enhancements | [N] | [N] | [N] | [N] | [N] |
| **Total** | **[N]** | **[N]** | **[N]** | **[N]** | **[N]** |
```

After saving, confirm:

```
✓ Saved to Backlog/Backlog-Raw/[MODULE_NAME]_Backlog.md

Next step options:
  A → Ingest into Backlog Wiki now
  B → Done for now, I'll ingest later

What would you like to do?
```

---

## Phase 2 — Ingest into Backlog Wiki

Triggered by:
- Choosing option A after retrieval
- "Ingest [module] backlog into wiki"
- "Ingest Backlog-Raw/[module] into Backlog Wiki"

### Source & Target Rules
- **Source:** `Backlog/Backlog-Raw/[MODULE_NAME]_Backlog.md` — READ ONLY, never modify
- **Target:** `Backlog/Backlog-Wiki/[MODULE_NAME].md` — one single file per module
- If the target file already exists, replace it entirely — do not append

---

### Step 1 — Read the Raw File Fully

Read every item in the raw file completely before writing anything.
Understand the full scope, hierarchy, and relationships across all item types.
Never truncate, summarize, or skip any field during ingestion.

---

### Step 2 — Write One Business Knowledge Document

Save to: `Backlog/Backlog-Wiki/[MODULE_NAME].md`

The document must read as a coherent business knowledge article about the module —
written the way a senior PM would document the module for a new team member.
All work items are synthesized into a unified narrative, but every item's full
detail is preserved exactly. Nothing is shortened.

```markdown
---
title: [MODULE_NAME] — Business Knowledge
type: business-knowledge
module: [MODULE_NAME]
source: Backlog-Raw/[MODULE_NAME]_Backlog.md
azure-items-total: [N]
epics: [N]
features: [N]
pbis: [N]
bugs: [N]
enhancements: [N]
open-points: [N]
last-ingested: YYYY-MM-DD
iteration: [N]
---

# [MODULE_NAME]

## Overview
[Claude writes a 3-5 sentence synthesis of what this module is, what business
problem it solves, who it serves, and its current state — derived from reading
all Epics and their descriptions together. This is synthesized, not copied.]

---

## Business Scope

[For each Epic, write a section using the actual business topic as the heading.
Never use "Epic", "Feature", or ticket IDs as headings — use the real business
concept the item represents. Each section flows as a continuous business narrative.]

### [Business Area — derived from Epic Title]

[Full description of the Epic written in business language — what problem it
solves, what value it delivers, who it affects, and its current state.
Preserve every detail from the raw description.]

---

#### [Sub-topic — derived from Feature Title]

[Full description of the Feature in business language — what it enables,
who it serves, how it relates to the Epic above, and its current state.
Preserve every detail from the raw description and acceptance criteria.]

---

**Business Requirements**

[Every PBI under this Feature. Each written as a full standalone business
requirement. Preserve all descriptions and acceptance criteria completely —
never summarize or shorten any field.]

> **[PBI Title]**
> [Full PBI description — every word from the raw file]
>
> **Acceptance Criteria:**
> [Full acceptance criteria — every point, every line, nothing omitted]
>
> **State:** [state] | **Priority:** [priority] | **Azure ID:** [ID]

[repeat the above block for every PBI under this Feature]

---

**Enhancements**

[Every Enhancement under this Feature. Each written as a full business
improvement requirement — what currently exists, what should change,
and the full expected outcome. Preserve all details completely.]

> **[Enhancement Title]**
> [Full enhancement description — every word from the raw file]
>
> **Expected Outcome:**
> [Full acceptance criteria or expected behavior — every point, nothing omitted]
>
> **State:** [state] | **Priority:** [priority] | **Azure ID:** [ID]

[repeat the above block for every Enhancement under this Feature]

---

**Known Issues**

[Every Bug under this Feature. Each written as a fully documented product issue —
current behavior, expected behavior, and resolution status.
Preserve all details completely.]

> **[Bug Title]**
> **Current behavior:** [full bug description — every word from the raw file]
>
> **Expected behavior / Fix criteria:**
> [Full acceptance criteria, steps to reproduce, or expected resolution — nothing omitted]
>
> **State:** [state] | **Priority:** [priority] | **Azure ID:** [ID]

[repeat the above block for every Bug under this Feature]

---

[repeat the full #### Feature block — Requirements, Enhancements, Known Issues —
for every Feature under this Epic]

---

[repeat the full ### Epic section for every Epic in the module]

---

## Open Points

[All unresolved questions, TBDs, pending decisions, or items flagged as needing
clarification — extracted from anywhere across all Epics, Features, PBIs,
Bugs, and Enhancements. Every open point is listed here so nothing is buried.]

| # | Open Point | Source (ID — Title) | Type | Priority |
|---|-----------|---------------------|------|----------|
| 1 | [full question or TBD text] | [ID — title] | [Epic/Feature/PBI/Bug/Enhancement] | [priority] |

---

## Current Status Summary

| Type | Total | New | Active | Resolved | Closed |
|------|-------|-----|--------|----------|--------|
| Epics | [N] | [N] | [N] | [N] | [N] |
| Features | [N] | [N] | [N] | [N] | [N] |
| Product Backlog Items | [N] | [N] | [N] | [N] | [N] |
| Enhancements | [N] | [N] | [N] | [N] | [N] |
| Bugs | [N] | [N] | [N] | [N] | [N] |
| **Total** | **[N]** | **[N]** | **[N]** | **[N]** | **[N]** |
```

---

### Step 3 — Update `Backlog-Wiki/index.md`

If index.md doesn't exist, create it. If the module already exists as a row,
update it. Never duplicate rows.

```markdown
# Backlog Wiki Index

| Module | Last Ingested | Azure Items | Open Points | File |
|--------|--------------|-------------|-------------|------|
| [MODULE_NAME] | YYYY-MM-DD | [N] | [N] | [[MODULE_NAME]] |
```

---

### Step 4 — Append to `Backlog-Wiki/log.md`

```
## [YYYY-MM-DD] ingest | [MODULE_NAME] | [N] azure items → 1 business knowledge document | iteration [N]
```

---

### Step 5 — Confirm

```
✓ Backlog Wiki updated

  File: Backlog-Wiki/[MODULE_NAME].md
  Azure items ingested: [N]
    - Epics: [N]
    - Features: [N]
    - Product Backlog Items: [N]
    - Enhancements: [N]
    - Bugs: [N]
  Open points captured: [N]
  Iteration: [N]

Ready to run a gap analysis?
Say "run gap analysis for [MODULE_NAME]" or load skill: backlog-gap-analysis
```

---

## Iteration Support

### Re-retrieving an Existing Module
Triggers: "re-retrieve [module] backlog", "refresh [module] backlog from Azure"

1. Retrieve fresh data from Azure DevOps
2. Show summary and diff vs previous raw file:
```
Changes since last retrieval ([previous date]):
  New items:          [N] — [list titles]
  State changes:      [N] — [list]
  Closed/Removed:     [N] — [list]
  Description updates:[N] — [list]
```
3. Wait for confirmation before overwriting the raw file
4. Increment `iteration` in frontmatter
5. After saving, offer to re-ingest into Backlog Wiki

### Re-ingesting Without Re-retrieving
Triggers: "re-ingest [module] backlog", "update [module] business knowledge"

1. Read existing `Backlog-Raw/[MODULE_NAME]_Backlog.md`
2. Show what will change vs existing wiki document
3. Wait for confirmation
4. Rewrite `Backlog-Wiki/[MODULE_NAME].md` fully with updated content
5. Increment `iteration` in frontmatter
6. Append to log

### Checking a Specific Item
Triggers: "show me [ID] in the [module] backlog"
→ Read `Backlog-Raw/[MODULE_NAME]_Backlog.md`, find the item, display it in full
→ Do not modify any file

---

## Update SKILLS-README.md Entry

```markdown
| azure-backlog-retrieval.md | Retrieve Epics, Features, PBIs, Bugs, and Enhancements from Azure DevOps for a specific module. Saves full structured raw file to Backlog/Backlog-Raw/ then synthesizes everything into one unified business knowledge document in Backlog/Backlog-Wiki/ — all detail preserved, written as business narrative. Supports re-retrieval with diff, re-ingestion, and iteration. No file is written without explicit confirmation. |
```