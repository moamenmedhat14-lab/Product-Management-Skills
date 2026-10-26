---
name: story-writer
description: Analysis, ticket drafting, and publishing skill — retrieves context from the wiki and manages the ticket lifecycle from draft to a tickets/ folder nested inside the related work-bench/[Analysis_Name]/ folder.
allowed-tools: Read, Glob, Grep, Bash, GenerateText, Write
argument-hint: "feature name" | "module topic" | ANA-1 | TKT-1
---

# LLM Story Writer & Analyzer Skill

## Analysis-First Ticket Generation & Management

This skill analyzes the `wiki/` folder to synthesize development-ready tickets and manages their lifecycle through review to publishing.

---

## SEARCH SOURCE RULE — all requirements must come from wiki/ or the matching work-bench/ FAD

```
STRICT REQUIREMENT CONSTRAINT:
1. Derive all ticket logic, fields, and constraints ONLY from:
     a. wiki/ pages, and/or
     b. the work-bench/[Analysis_Name]/analysis/ FAD matching the requested feature
        (used when the feature has not yet been ingested into wiki/, or to fill
        detail wiki/ doesn't have yet).
2. Do NOT use training knowledge to fill gaps or invent system behavior.
3. Every requirement in the generated ticket should be traceable to a wiki page
   or to the work-bench/[Analysis_Name]/analysis/ FAD — cite which source line/section
   it came from when the two sources disagree.
4. If neither source has information needed for a complete ticket:
     State the gap explicitly in the "Note:" section.
     Do NOT make assumptions or "hallucinate" the missing logic.
5. If wiki/ and the work-bench/ FAD contradict each other on the same rule,
   flag it explicitly in the ticket's "Note:" section instead of silently picking one.
6. Search ONLY wiki/ and the matching work-bench/[Analysis_Name]/analysis/ folder.
   Ignore raw/ and other directories.
7. WRITE RULE: The skill is strictly read-only for wiki/, raw/, and work-bench/[Analysis_Name]/analysis/.
   It is authorized to write and create directories ONLY within the
   work-bench/[Analysis_Name]/tickets/ directory.
```

---

## What this skill does

- Performs a 3-pass retrieval on `wiki/` (and the matching `work-bench/[Analysis_Name]/analysis/` FAD when the feature isn't in wiki/ yet, or to fill in detail wiki/ lacks) to gather all context for a feature or module.
- Analyzes the gathered content for logical consistency and ticket-readiness.
- Generates highly structured tickets based *only* on verified wiki content.
- **Manages Ticket Lifecycle**: Handles the review process and automated publishing to the `work-bench/[Analysis_Name]/tickets/` directory, linked to the analysis it belongs to.
- Identifies and surfaces gaps where the wiki lacks the detail required for implementation.

---

## Core approach: 3-pass retrieval + analysis

### Retrieval Phase (Internal)
Before generating any content, the skill must:
1. **Pass 1:** Scan `wiki/index.md` for the topic and candidate pages.
2. **Pass 2:** Read candidate pages in full.
3. **Pass 3:** Follow central wikilinks to resolve dependencies (e.g., cross-module logic).
4. **Pass 4:** Locate `work-bench/[Analysis_Name]/analysis/` for the same feature (sanitized name match). If found, read it in full — use it to fill any detail the wiki/ pages don't cover, or as the primary source if the feature hasn't been ingested into wiki/ yet.

### Analysis Phase
Evaluate the retrieved data against Ticket standards:
- **Acceptance Criteria completeness**: Are all sections below defined?
- Are "Access" paths defined?
- Are "Validations" explicit and testable?
- Are "State Changes" and "Upon saving" effects clear?
- If logic is missing, mark it as a **GAP**.

---

## Operations

### ANA-1: Feature Readiness Analysis
**Trigger:** The user wants to know if a feature is well-defined enough for a ticket.

```
1. Search wiki/ using the 3-pass retrieval pattern for [topic].
2. Identify:
   - Covered requirements (fields, logic, transitions)
   - Missing requirements (what's needed to write a full ticket)
   - Contradictions (conflicting rules found in different pages)
3. Return a "Readiness Report" instead of a ticket.
```

### TKT-1: Generate, Review & Publish
**Trigger:** The user wants a development-ready ticket for a feature.

1. **Context Retrieval**: Perform the 3-pass retrieval on `wiki/`.
2. **Drafting**: Construct the ticket using the Standard Template (ensuring the `Acceptance Criteria:` section is complete).
3. **Locate the Related Analysis Folder**:
   - Sanitize the [feature-name] for directory naming (e.g., "Shift Scheduling" -> `Shift-Scheduling`).
   - List the folders under `work-bench/` and look for one matching the sanitized [feature-name] (exact or close match) — this is `[Analysis_Name]`.
   - If no matching folder exists, **ask the user explicitly**: "I couldn't find an existing analysis folder for '[feature-name]' under `work-bench/`. Which analysis folder should these tickets be linked to? (Or should I create a new `work-bench/[feature-name]/` folder for them?)" Do not guess.
4. **Confirmation Loop**:
   - Present the full ticket content to the user.
   - **Ask explicitly**: "Review the ticket above. Should I proceed to publish this to `work-bench/[Analysis_Name]/tickets/`?"
5. **Automated Publishing (Post-Approval)**:
   - Create the directory `work-bench/[Analysis_Name]/tickets/` if it does not exist.
   - Save the ticket file using the naming convention: `[Ticket-ID]-[Sanitized-Title].md`.
   - **Pre-Publishing Check**: Before saving, check if a ticket with the same ID or name already exists in the `work-bench/[Analysis_Name]/tickets/` folder and notify the user if a conflict is found.

**Folder Structure:** Tickets are published as a sibling of the `analysis/` folder inside the same analysis's workbench folder:
```
work-bench/
  [Analysis_Name]/
    analysis/
      [Analysis_Name]_Analysis.md
    tickets/
      [Ticket-ID]-[Sanitized-Title].md
```

---

## Standard Ticket Template

Every generated ticket MUST strictly follow this layout.

```text
Ticket Name: [Category][Module] Title

As a <user>,
I want to <action>,
So that <benefit>.

[Design Link]

Acceptance Criteria:

Access:
<Navigation path 1> --> <Sub-path>

<Updates / Steps / Update>:
<Rule or behavior 1>
<Rule or behavior 2>
<Field name> "<Type, constraints>"

If <condition>:
<expected behavior>

Validations:
- <Explicit, testable rule 1>
- <Explicit, testable rule 2>

Upon <action / saving>:
<System result / downstream impact 1>
<State transition logic (Previous State -> New State)>

Note:
<Critical cross-feature dependency or explicit gap found in wiki>
```

---

## Writing Style Rules — Moamen's ticket style

Reference ticket: `work-bench/assignment_log_list/tickets/tkt_1_assignment_log_list.md` (hand-optimized by Moamen). Match its layout and tone.

- **Ticket Name**: `[Module][Feature] Title`. Use the same module name as the Access path (for example `[Staff Management]`).
- **Story**: bold the three keywords: `**As** a ...,` / `**I want to** ...,` / `**So that** ...`.
- **Acceptance Criteria**: All content after the `[Design Link]` marker is the Acceptance Criteria. Always include the `Acceptance Criteria:` header.
- **Short and direct**: One rule per line. No long paragraphs. Do not repeat the same rule in two sections.
- **Bullets and nesting**: Use `-` bullets. Nest detail under the item it explains (use one extra tab level). Example: a column bullet with its rule nested under it.
- **Access**: A bullet with the navigation path, for example `- Staff Management --> Assignment Log`.
- **Group the steps by topic** with short headers, for example `Updates / Steps:`, `Validations:`, `Filters:`, `Upon-Saving:`, `Screen Controls:`, `Special Cases:`. Use only the groups the ticket needs. Put the Access path first and `Special Cases:` last.
- **Fields and filters**: Name the field, then put the type or constraint in double quotes, for example `Date range "Start date and end date, applied on the Effective date column"` or `Tags "only permitted unit's tags"`. Put the extra rule as a nested bullet.
- **Conditional logic**: Use `If <condition>:` followed by `-` bullets.
- **Validations**: `-` bullets. Put an example as a nested bullet under the rule it explains.
- **Terminology**: Use the exact terms of the wiki or the FAD (for example "First assignment", "From unit", "To unit").
- **Wording**: Simple, plain sentences (Simplified Technical English). Use "can" and "must". Avoid "should".
- **Keep it small**: Do not add rules that the user did not ask for. Out-of-scope items go in `Note:` only if the user wants them there.
- **Learn from edits**: If Moamen edits a published ticket by hand, read the edit. Apply the same style change to the next tickets.

---

## Skill responsibilities at a glance

| Operation | Purpose | Search Scope |
|-----------|---------|--------------|
| `/story-writer ANA-1` | Analyze if wiki has enough info for a ticket | `wiki/` + matching `work-bench/[Analysis_Name]/analysis/` |
| `/story-writer TKT-1` | Generate, Review, and Publish a ticket | `wiki/` + matching `work-bench/[Analysis_Name]/analysis/` |
| `/story-writer [topic]` | Default: Search + Analyze + Draft + Review | `wiki/` + matching `work-bench/[Analysis_Name]/analysis/` |
