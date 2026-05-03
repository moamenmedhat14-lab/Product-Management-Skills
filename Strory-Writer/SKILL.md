---
name: story-writer
description: Analysis and ticket drafting skill — performs structured retrieval from the wiki to analyze feature readiness and generate tickets. Read-only; only searches the wiki/ folder.
allowed-tools: Read, Glob, Grep, Bash, GenerateText
argument-hint: "feature name" | "module topic" | ANA-1 | TKT-1
---

# LLM Story Writer & Analyzer Skill

## Analysis-First Ticket Generation

This skill analyzes the `wiki/` folder to synthesize development-ready tickets. 
It is strictly **read-only** and adheres to the **SEARCH SOURCE RULE**: it only 
uses information present in the synthesized wiki.

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
```

---

## What this skill does

- Performs a 3-pass retrieval on `wiki/` to gather all context for a feature or module.
- Analyzes the gathered content for logical consistency and ticket-readiness.
- Generates highly structured PM-style tickets based *only* on verified wiki content.
- Identifies and surfaces gaps where the wiki lacks the detail required for implementation.
- Operates as a "read-only" researcher — it never modifies the wiki or raw documents.

---

## Core approach: 3-pass retrieval + PM analysis

### Retrieval Phase (Internal)
Before generating any content, the skill must:
1. **Pass 1:** Scan `wiki/index.md` for the topic and candidate pages.
2. **Pass 2:** Read candidate pages in full.
3. **Pass 3:** Follow central wikilinks to resolve dependencies (e.g., cross-module logic).

### Analysis Phase
Evaluate the retrieved data against PM's Ticket standards:
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

### TKT-1: Generate Ticket from Wiki
**Trigger:** The user wants a development-ready ticket for a feature.

```
1. Search wiki/ using the 3-pass retrieval pattern for [topic].
2. Construct the ticket using the Standard Template (PM's Style).
3. Ensure every line is derived from the wiki.
4. If info is missing for a section (e.g., Design Link), use the placeholder [Design Link].
5. If business logic is missing, add a "Note:" stating the gap.
```

---

## Standard Ticket Template (PM's Style)

Every generated ticket MUST strictly follow this layout.

```text
Ticket Name: [Category][Module] Title

As a <user>,
I want to <action>,
So that <benefit>.

[Design Link]

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
| `/story-writer TKT-1` | Generate a ticket based on wiki content | `wiki/` only |
| `/story-writer [topic]` | Default: Search + Analyze + Draft Ticket | `wiki/` only |

