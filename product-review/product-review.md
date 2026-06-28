# Skill: Product Review

**Skill ID:** product-review
**Version:** 1.0
**Triggers:** "run product review", "start product review", "review [module/feature]",
              "heuristic review", "product review for [name]"

---

## Purpose
Run a structured product review combining Nielsen's 10 Usability Heuristics with a
Logic Model analysis. Claude assesses each point first, then iterates with the PM
until finalized. All output is saved to the project's Product-Review/ folder.

---

## Severity Scale (reference throughout)
- **0** — Looks good, no issue
- **1** — Minor, low priority
- **2** — Moderate, should fix
- **3** — Major, high priority
- **4** — Blocker, must fix before release

---

## Step 0 — Session Setup

Before anything else, ask exactly this:

---
**Claude asks:**
```
Starting a Product Review session.

1. Which module or feature are we reviewing?
2. Do you have a Figma link to share? (optional — paste it or skip)

Once you answer, I'll begin the Heuristic Evaluation.
```
---

Wait for the answer. Do not proceed until the module name is confirmed.

If a Figma link is provided:
- Access it using the Figma MCP
- Read the relevant frames/flows for the named module
- Use this as visual evidence throughout the review

If no Figma link is provided:
- Rely on the project wiki (wiki/features/, wiki/user-flows/) for context
- Note in the output that no Figma was reviewed

Store internally:
- `[MODULE_NAME]` = the confirmed feature/module name
- `[FIGMA_LINK]` = the link or "Not provided"
- `[REVIEW_DATE]` = today's date (YYYY-MM-DD)

---

## Step 1 — Heuristic Evaluation (10 points, one at a time)

Work through all 10 heuristics sequentially. Never show more than one at a time.

### Protocol per heuristic:

1. Claude reads the relevant wiki pages and/or Figma frames
2. Claude presents its own assessment first using the format below
3. Wait for the PM to respond — they may agree, correct, add context, or ask to iterate
4. Iterate until the PM says "finalized", "next", "done", or "move on"
5. Lock the heuristic and move to the next one

### Assessment format (use for each of the 10 heuristics):

---
```
## Heuristic [N] — [Heuristic Name]
**Review question:** [question from template]

**My assessment:**
[Claude's observation based on wiki/Figma — 2-4 sentences describing what it sees,
what works, and what concerns exist. Be specific to the actual module being reviewed,
not generic.]

**Suggested severity:** [0–4] — [one-line reason]

**Recommendation:** [specific, actionable fix or confirmation that it's good]

---
Does this match what you're seeing? You can:
- Agree → I'll lock this and move to the next heuristic
- Correct my observation → tell me what's different
- Add context → share more detail and I'll revise
- Change the severity → tell me the right level
```
---

After the PM finalizes each heuristic, store internally:
```
H[N]: observation=[text], severity=[0-4], recommendation=[text], status=finalized
```

### The 10 Heuristics in Order:

**H1 — Visibility of System Status**
Review question: Does the system keep users informed with timely, clear feedback?
Focus: loading states, progress indicators, confirmations, error states, empty states

**H2 — Match Between System and the Real World**
Review question: Does the product use familiar language, concepts, and industry standards?
Focus: terminology, icons, mental models, labeling, metaphors used in the UI

**H3 — User Control and Freedom**
Review question: Can users undo, cancel, go back, or safely exit unwanted actions?
Focus: undo/redo, back navigation, cancel flows, destructive action confirmations

**H4 — Consistency**
Review question: Are words, interactions, and visual patterns consistent within the platform?
Focus: button styles, naming conventions, interaction patterns, component reuse

**H5 — Error Prevention**
Review question: Does the design prevent mistakes before they happen?
Focus: inline validation, confirmation dialogs, constraints, default values, guardrails

**H6 — Recognition Rather Than Recall**
Review question: Are options, information, and context visible when needed?
Focus: visible affordances, contextual help, visible options vs hidden menus

**H7 — Flexibility and Efficiency of Use**
Review question: Does the product support both novice and power users efficiently?
Focus: shortcuts, bulk actions, defaults vs advanced settings, onboarding vs expert mode

**H8 — Aesthetic and Minimalist Design**
Review question: Is the interface focused on what matters, without irrelevant clutter?
Focus: information hierarchy, visual noise, secondary vs primary actions, white space

**H9 — Help Users Recognize, Diagnose, and Recover from Errors**
Review question: Are errors written clearly and paired with recovery actions?
Focus: error message copy, specificity of errors, recovery CTAs, error placement

**H10 — Help and Documentation**
Review question: Is help easy to find, context-aware, and action-oriented?
Focus: tooltips, empty states guidance, help center links, onboarding flows, FAQs

---

After H10 is finalized, say:
```
✓ Heuristic Evaluation complete — 10/10 finalized.
Moving to the Logic Model now.
```

---

## Step 2 — Logic Model: Product Value Creation Map

Work through the 4 Logic Model components sequentially, one at a time.
Same protocol as Step 1 — Claude assesses first, PM iterates, then lock and move on.

### Assessment format (use for each component):

---
```
## Logic Model — [Component Name]
**What this captures:** [one line explanation of the component]

**My assessment:**
[Claude's analysis specific to [MODULE_NAME] — what inputs/actions/outputs/factors
it can identify from the wiki, tickets, and Figma. Be concrete.]

---
Does this match your understanding? You can agree, correct, or add to it.
Say "next" when ready to move on.
```
---

### The 4 Logic Model Components in Order:

**LM1 — Inputs → Actions → Outputs**

| Component | What Claude assesses |
|-----------|---------------------|
| Inputs | Data, APIs, content, people, budget, policies, tools feeding this module |
| Actions | What the product does: collect, validate, recommend, personalize, notify, automate |
| Outputs | Direct deliverables: completed task, saved profile, generated report, resolved issue |

Present all three (Inputs / Actions / Outputs) together as one assessment block.
Iterate together until finalized.

**LM2 — Assumptions**
What must be true about users, data, behavior, operations, or adoption for this module
to work as intended? Claude lists what it infers from the wiki and tickets.

**LM3 — External Factors**
Market forces, regulation, seasonality, competition, dependencies, or platform constraints
that could affect this module. Claude surfaces these from discovery wiki if available.

**LM4 — Decision**
Based on the full logic model: Accept risk / Validate assumption / Redesign.
Claude gives a recommended decision with reasoning. PM finalizes.

---

After LM4 is finalized, say:
```
✓ Logic Model complete.
Generating final synthesis now.
```

---

## Step 3 — PM Synthesis (auto-generated, no iteration needed)

Once both Step 1 and Step 2 are fully finalized, auto-generate the synthesis.
Do not ask for iteration here — generate it and present it, then save it.

### Prioritized Recommendations
Compile all H1–H10 recommendations. Sort by severity descending (4 first, 0 last).
For equal severity, order by estimated user impact.

Format:
```
| Rank | Recommendation | Severity | Impact | Status |
|------|---------------|----------|--------|--------|
| 1    | [text]        | [0-4]    | [High/Med/Low] | Open |
...
```

### Open Questions
From the full review, list any unresolved questions — things that came up during
iteration that need user research, data, or stakeholder input to answer.

Format:
```
| # | Question | Owner | Priority |
|---|----------|-------|----------|
| 1 | [text]   | PM / Research / Design / Eng | High/Med/Low |
```

### Action Points
Concrete next steps derived from the review. Each must have an owner type.

Format:
```
| # | Action | Owner | Linked To |
|---|--------|-------|-----------|
| 1 | [text] | PM / Design / Eng | H[N] or LM[N] |
```

---

## Step 4 — Save Output

### Determine Output Path

Check if `Product-Review/` folder exists at the project root.
- If not → create it
- Always save there regardless

Output folder naming:
```
[project-root]/Product-Review/[MODULE_NAME]_Product_Review/
```

Spaces in module name → replace with underscores.
Example: "Checkout Flow" → `Checkout_Flow_Product_Review/`

### Files to Write

**File 1: `[MODULE_NAME]_Product_Review.md`** — the complete review document

Structure:
```markdown
---
title: [MODULE_NAME] — Product Review
date: [REVIEW_DATE]
reviewer: Moamen
figma: [FIGMA_LINK]
heuristics-finalized: 10/10
logic-model-finalized: 4/4
---

# [MODULE_NAME] — Product Review
**Date:** [REVIEW_DATE]
**Figma:** [FIGMA_LINK or "Not provided"]

---

# 1. Heuristic Evaluation

[All 10 finalized heuristics — observation, severity, recommendation]

---

# 2. Logic Model

[All 4 finalized components]

---

# 3. PM Synthesis

## Prioritized Recommendations
[table]

## Open Questions
[table]

## Action Points
[table]

---

# References
Nielsen Norman Group — Heuristic Evaluation Workbook:
https://media.nngroup.com/media/articles/attachments/Heuristic_Evaluation_Workbook_1_Fillable.pdf

Logic models and system thinking in service design:
https://medium.com/@DrUrvashi.Sharma/using-logic-models-as-agents-of-system-thinking-in-service-design-e4175aba5633
```

**File 2: `iteration-log.md`** — tracks future iterations on this review

```markdown
# Iteration Log — [MODULE_NAME] Product Review

## Review 1 — [REVIEW_DATE]
- Heuristics reviewed: 10/10
- Logic model components: 4/4
- Critical findings: [count of severity 3-4]
- Open questions: [count]
- Action points: [count]
```

### After saving, confirm:
```
✓ Product Review saved to:
  Product-Review/[MODULE_NAME]_Product_Review/

Files created:
  - [MODULE_NAME]_Product_Review.md
  - iteration-log.md

Summary:
  - [N] heuristic findings ([N] critical, [N] major, [N] moderate)
  - [N] open questions
  - [N] action points

Want to run another review or iterate on this one?
```

---

## Iteration on an Existing Review

Triggers: "iterate on [module] review", "update the [module] review", "revise [module] review"

1. Read `Product-Review/[MODULE_NAME]_Product_Review/[MODULE_NAME]_Product_Review.md`
2. Read `iteration-log.md`
3. Ask: "Which part do you want to revisit — a specific heuristic, the logic model, or the full synthesis?"
4. Re-run only the requested section using the same step-by-step protocol
5. Update the main review file with revised content
6. Append to iteration-log.md:

```markdown
## Review [N] — [DATE]
- Triggered by: [what changed]
- Sections revised: [list]
- Changes: [summary]
```

---

## Update SKILLS-README.md Entry

Add this row to `.pm-skills/SKILLS-README.md`:

```markdown
| product-review.md | Run a structured product review combining Nielsen's 10 Heuristics + Logic Model. Claude assesses each point first, PM iterates to finalize, then saves a complete report to Product-Review/ at the project level. |
```