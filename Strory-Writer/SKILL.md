---
name: story-writer
description: Analysis, ticket drafting, and publishing skill — retrieves context from the wiki and manages the ticket lifecycle from draft to the tickets/ folder.
allowed-tools: Read, Glob, Grep, Bash, GenerateText, Write
argument-hint: "feature name" | "module topic" | ANA-1 | TKT-1
---

# LLM Story Writer & Analyzer Skill

## Analysis-First Ticket Generation & Management

This skill analyzes the `wiki/` folder to synthesize development-ready tickets and manages their lifecycle through review to publishing.

---

## SEARCH SOURCE RULE — all requirements must come from wiki/

```
STRICT REQUIREMENT CONSTRAINT:
1. Derive all ticket logic, fields, and constraints ONLY from wiki/ pages.
2. Do NOT use training knowledge to fill gaps or invent system behavior.
3. Every requirement in the generated ticket should be traceable to a wiki page.
4. If the wiki is missing information needed for a complete ticket:
     State the gap explicitly in the "Note:" section.
     Do NOT make assumptions or "hallucinate" the missing logic.
5. Search ONLY the wiki/ folder. Ignore raw/ and other directories.
6. WRITE RULE: The skill is strictly read-only for wiki/ and raw/. It is authorized to write and create directories ONLY within the tickets/ directory.
```

---

## What this skill does

- Performs a 3-pass retrieval on `wiki/` to gather all context for a feature or module.
- Analyzes the gathered content for logical consistency and ticket-readiness.
- Generates highly structured tickets based *only* on verified wiki content.
- **Manages Ticket Lifecycle**: Handles the review process and automated publishing to the `tickets/` directory.
- Identifies and surfaces gaps where the wiki lacks the detail required for implementation.

---

## Core approach: 3-pass retrieval + analysis

### Retrieval Phase (Internal)
Before generating any content, the skill must:
1. **Pass 1:** Scan `wiki/index.md` for the topic and candidate pages.
2. **Pass 2:** Read candidate pages in full.
3. **Pass 3:** Follow central wikilinks to resolve dependencies (e.g., cross-module logic).

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
3. **Confirmation Loop**:
   - Present the full ticket content to the user.
   - **Ask explicitly**: "Review the ticket above. Should I proceed to publish this to `tickets/[feature-name]/`?"
4. **Automated Publishing (Post-Approval)**:
   - Sanitize the [feature-name] for directory naming (e.g., "Shift Scheduling" -> `shift-scheduling`).
   - Create the directory `tickets/[sanitized-feature-name]/` if it does not exist.
   - Save the ticket file using the naming convention: `[Ticket-ID]-[Sanitized-Title].md`.
   - **Pre-Publishing Check**: Before saving, check if a ticket with the same ID or name already exists in the `tickets/` folder and notify the user if a conflict is found.

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

## Writing Style Rules

- **Acceptance Criteria**: All content following the `[Design Link]` marker (including Access, Steps, Validations, Upon saving, and Note) collectively constitutes the **Acceptance Criteria**. When generating tickets, always include the `Acceptance Criteria:` header.
- **Direct & Instructional:** Use "will", "must", or "can". Avoid "should".
- **No Bullets in Logic:** Use a line-by-line structure for rules (each line is a rule).
- **Terminology:** Use exact wiki terms (e.g., "License record", not "License").
- **Fields:** Format as `Field name "Type, constraints"`.
- **Conditional Logic:** Use `If <condition>:` blocks.

---

## Skill responsibilities at a glance

| Operation | Purpose | Search Scope |
|-----------|---------|--------------|
| `/story-writer ANA-1` | Analyze if wiki has enough info for a ticket | `wiki/` only |
| `/story-writer TKT-1` | Generate, Review, and Publish a ticket | `wiki/` only |
| `/story-writer [topic]` | Default: Search + Analyze + Draft + Review | `wiki/` only |
