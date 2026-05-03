---
name: feature-analyzer
description: Systemic analysis and feature breakdown skill — performs structured retrieval from the wiki to analyze cross-module dependencies, system reflections, and user flows. Read-only; only searches the wiki/ folder.
allowed-tools: Read, Glob, Grep, Bash, GenerateText
argument-hint: "epic name" | "feature topic" | ANA-1 | FEAT-1
---

# LLM Epic & Feature Analyzer Skill

## Systemic Feature Breakdown & Impact Analysis

This skill analyzes the `wiki/` folder to deconstruct high-level epics and features into rigorous, systemic breakdowns. It maps affected areas, requirement impacts, user flows, and cross-functional dependencies. It is strictly **read-only** and adheres to the **SEARCH SOURCE RULE**.

---

## SEARCH SOURCE RULE — all requirements must come from wiki/

```text
STRICT REQUIREMENT CONSTRAINT:
1. Derive all flows, system reflections, and dependencies ONLY from wiki/ pages.
2. Every impacted area MUST be traced back to a specific requirement ID (e.g., R.S#2.1) found in the wiki.
3. Do NOT use training knowledge to fill gaps or invent system behavior.
4. If the wiki is missing information needed for a complete flow or dependency map:
     State the gap explicitly. Do NOT make assumptions or "hallucinate" the missing logic.
5. Search ONLY the wiki/ folder. Ignore raw/ and other directories.

## Output Style — RSD Structure

The analysis output must follow the same structural style used by the raw RSD document in `raw/NERA _ Admin Portal RSD.md`:
- Use clear section headings and numbered requirements.
- Preserve the RSD-like tone: concise requirement statements, validations, and expected outcomes.
- Include explicit references to requirement IDs where available (e.g. R.S#20.1, R.S#14.6).
- Use tables or bullet lists to present feature flows, validations, and impacted areas.
- If the wiki lacks detail for a required section, state the gap explicitly rather than inventing content.

## Operation Modes

### ANA-1: Systemic Analysis
- Identify the feature or epic requested.
- Gather all relevant wiki pages and requirement IDs.
- Map the flow, dependencies, and impacted modules.
- Output findings using RSD-like structure and headings.

### FEAT-1: Feature Breakdown
- Provide a structured feature analysis with:
  - Purpose
  - Scope
  - Steps / flow
  - Validations
  - Reflections / impacted systems
  - Gaps and open questions
- Keep the output aligned with raw RSD format and explicit references.

## Important Rules

- Do not use raw/ content as a source for requirements unless it is already reflected in wiki/.
- Do not invent R.S# numbers; only use those present in the wiki.
- When the wiki does not define a behavior, report the missing requirement as a gap.
- Use only the allowed tools listed in the header.
