# Discovery Drafter - Document Templates

Use these exactly. Omit any section that has no approved content (no "N/A" stubs).

---

## 00 Project Brief

```markdown
# [Project Name] - Project Brief

**Purpose:** [one line]

## Objective & Success Metrics
- **Objective:** [business outcome]
- **Success metrics:** [metric - target - how measured]

## Scope
- **In scope:** [...]
- **Out of scope:** [...]
- **Later phase:** [...]

## Users & Roles
| Role | Description | Key goals |
|------|-------------|-----------|

## Known Integrations
| System | Direction (In/Out/Both) | Data exchanged | Sync/Async | Owner | Contract status |
|--------|------------------------|----------------|------------|-------|-----------------|

## Competitor Insights
| Competitor | Capability / pattern worth noting | Relevance to us | Source | Source type | Confidence |
|------------|-----------------------------------|-----------------|--------|-------------|------------|
| Vendor X | Shift swaps need supervisor approval within 24h | Mirrors our F-4.3 | sources/competitor-evidence/Vendor X - Shift Swap.md | Primary (teardown) | High |

Source type: `Primary`, `Primary (teardown)`, `NotebookLM ([notebook] -> [underlying doc])`, `Secondary`. Confidence: `High` / `Medium` / `Low` per the Cited research rule.

## Constraints
- [regulatory, platform, language/RTL, deadline, technical]
```

---

## 01 Feature List

```markdown
# [Project Name] - Feature List

**Summary:** [X] modules | [Y] features ([n] Stated, [n] Competitor, [n] Integration, [n] Implied) | MVP: [n]

## [Module Number]. [Module Name]
*[One line: what this module is for]*

| ID | Feature (verb + object) | Description | Source | Priority | Release | Depends on | Notes |
|----|-------------------------|-------------|--------|----------|---------|------------|-------|
| F-1.1 | Create Unit | Admin creates an operational unit with name, code and parent | Stated | Must | MVP | - | |
| F-1.2 | Bulk Import Units | Import units from Excel template | Competitor (X - sources/x-notes.md) | Could | Later | F-1.1 | [OQ-03] |
```

Rules: Source values are `Stated`, `Competitor (name - source)`, `Integration (system)`, `Implied (by F-x.y)`. Priority is MoSCoW. Only features the PM accepted are saved.

---

## 02 RSD

```markdown
# [Project Name] - Requirements Specification Document

**[Module Number]. [Module Name] Management:**
*Contextual description of the feature or module.*

**R.S#[Module].[Seq] | [Feature Action Name]**
*Feature: F-x.y | Roles: [roles allowed]*
* **Trigger / Input Fields:** ...
* **Validations:** ...
* **Upon Action:**
  * **User Feedback:** ...
  * **System Reflection:** ...
  * **Routing:** ...
  * **Logging:** ...
```

### Worked example

**1. Unit Management:**
*Units are the operational building blocks (e.g. sectors/positions) that staff are scheduled into. All scheduling, coverage and reporting modules reference units.*

**R.S#1.1 | Create New Unit**
*Feature: F-1.1 | Roles: System Admin*
* **Trigger / Input Fields:** "Add Unit" button on the Units list.
  * Unit Name (mandatory, free-text, max 100 chars)
  * Unit Code (mandatory, alphanumeric, max 10 chars, auto-uppercase)
  * Parent Unit (mandatory, single-select from active units)
  * Description [Optional] (free-text, max 500 chars)
* **Validations:**
  * Unit Code must be unique across all units, including inactive ones.
  * Unit Name must be unique within the same Parent Unit.
  * Parent Unit cannot be inactive.
  * Max hierarchy depth is 4 levels [OQ-04].
  * Save is blocked while mandatory fields are empty; inline error shown per field.
* **Upon Action:**
  * **User Feedback:** Success toast "Unit created successfully."; on duplicate code, inline error "This code is already used."
  * **System Reflection:** Unit added to the Units list as Active; becomes selectable in Shift Planning (R.S#4.1) and Coverage Dashboard filters (R.S#7.2).
  * **Routing:** Redirected to the new Unit's details screen.
  * **Logging:** Action "Unit Created", user, timestamp and all field values logged to the audit trail.

---

## 03 Open Questions

```markdown
# [Project Name] - Open Questions

**Summary:** [n] total | [n] blocking | Conflicts [n] - Gaps [n] - Assumptions [n] - Decisions [n] - Dependencies [n]

**Highest-impact decisions:**
1. [OQ-xx] - [one line]
2. ...

## [Module Number]. [Module Name]

| ID | Category | Related | Question / Conflict | Why it matters | Options | Recommended | Blocking | Owner | Status | Decision |
|----|----------|---------|---------------------|----------------|---------|-------------|----------|-------|--------|----------|
| OQ-04 | Assumption | F-1.1, R.S#1.1 | Is unit hierarchy capped at 4 levels? | Drives tree UI and report roll-ups | A) 4 levels B) Unlimited | A - matches current org chart; unlimited complicates roll-ups | No | PM | Open | |
| OQ-05 | Conflict | F-6.1, Brief scope | Brief puts mobile app out of scope, but push notifications are listed as Must | Can't deliver push without mobile | A) Email + in-app only B) Bring mobile into scope C) Defer push to Later | A | Yes | PM | Open | |

## Cross-cutting
[Questions not tied to one module]
```

Status values: `Open`, `Resolved`, `Deferred`. When Resolved, fill Decision and list the R.S# entries that were updated.

---

## 04 Decisions

```markdown
# [Project Name] - Decisions

## D-01 | [Short decision title]
*Date: YYYY-MM-DD | From: OQ-05 | Affects: F-6.1, R.S#6.1, R.S#6.2*

- **Context:** [what forced the decision, 1-3 lines]
- **Options considered:** A) ... B) ... C) ...
- **Decision:** [the chosen option, in the PM's exact wording for any numbers, ordering or "must not" rules]
- **Reasoning:** [why this over the alternatives]
- **Consequences:** [what becomes harder/easier, what is now out of scope, follow-ups]
- **Gate check:** Hard to reverse ✔ | Surprising without context ✔ | Real trade-off ✔
```

---

## 05 Glossary

```markdown
# [Project Name] - Glossary

| Term | Definition | Avoid (synonyms) | Arabic term [Optional] |
|------|------------|------------------|------------------------|
| Unit | An operational grouping that staff are scheduled into; units can nest under a parent unit. | Section, Department | وحدة |
| Coverage | The ratio of scheduled qualified staff to required staff for a unit and time slot. | Staffing level | |
```

Rules: terms only, one tight definition each, alphabetical within the file. No requirements, statuses' transition rules or notes.

---

## Questionnaire (export)

```markdown
# Questions for [Owner] - [Project Name]
*Prepared by: [PM name] | Date: YYYY-MM-DD | Please reply by: [date]*

[2-3 lines: what the project is, why we need these answers, how to answer - pick an option or write your own.]

## Blocking questions

### OQ-05 - [Question in plain language]
**Context:** [what we're designing and why this matters, no internal shorthand]
**Options:**
- A) ...
- B) ...
- C) ...
**Our recommendation:** A - [one-line reason]
**Your answer:**
> 

## Other questions
[same structure]
```

---

## 06 Session Handoff

```markdown
## Session YYYY-MM-DD
- **Phase:** [e.g. Phase 2 - RSD]
- **Last approved:** [e.g. RSD Module 3 - Shift Planning]
- **In progress (not approved, not saved):** [names only]
- **Blocking OQs:** [IDs]
- **Questionnaires awaiting answers:** [owner - date sent]
- **New this session:** Glossary [terms] | Decisions [D-IDs]
- **Next step:** [exact next action]
```

---

## Competitor Evidence (teardown)

```markdown
# [Competitor] - [Topic]
*Date: YYYY-MM-DD | Access: [how the PM has legitimate access] | Method: [web observation / HAR / APK resources] | Related: F-x.y*

| # | Observation | Evidence | Limitations |
|---|-------------|----------|-------------|
| 1 | Swap request form has a mandatory reason field (max 250 chars) | Screenshot of request form; field validation message observed | Observed on web only; mobile not checked |

**Takeaways for us (proposals only):**
- [proposal] -> Feature List as `Competitor` proposal / RSD as [OQ-xx]
```
