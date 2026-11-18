# Second Eye — Report Template

Path: `[Project]/work-bench/[Topic]/second-eye/Second Eye [Topic].md`

- Create the file at the first round, with the frontmatter and the H1.
- For each new round, add a new `## Round N — YYYY-MM-DD` section at the bottom. Do not change earlier rounds, except the Resolution Log of the current round.
- Omit sections that have no content (for example, no Wiki Conflicts). Do not write placeholder lines.

---

```markdown
---
status: Draft
---

# Second Eye — [Topic]

## Round 1 — YYYY-MM-DD

**Target:** [[path/to/target.md]] (Scope: full file | section "...")
**Baseline:** [[Project/wiki/index.md]], [[linked-file-1]], ...
**Lenses:** naive-reader, logic-critic, edge-case-hunter, actor-permission, lifecycle-state, zoom-out, dev-qa-readiness
**Result:** 🔴 X Blocker · 🟡 Y Major · 🟢 Z Nit

### 1. How a fresh reader understood it
> Check this first. If this paraphrase is not what you meant, the document does not say what you meant.

**What this proposes**
[naive-reader paraphrase, verbatim]

**How the existing system works, per this document**
[naive-reader paraphrase, verbatim]

### 2. Reviewer Status
| Lens | Status | Findings |
|------|--------|----------|
| naive-reader | completed / not run | n |
| logic-critic | completed / not run | n |
| edge-case-hunter | completed / not run | n |
| actor-permission | completed / not run | n |
| lifecycle-state | completed / not run | n |
| zoom-out | completed / not run | n |
| dev-qa-readiness | completed / not run | n |

### 3. Findings

#### 🔴 SE-1 — [Short title]
- **Type:** Gap | Edge Case | Contradiction | Ambiguity | Assumption | Wiki Conflict | Scope
- **Where:** [section heading / lines]
- **Raised by:** edge-case-hunter, actor-permission
- **Evidence:** "[quote]" or "not mentioned"
- **Finding:** [...]
- **Scenario:** Given ..., when ..., then the doc does not say ...
- **Why it matters:** [...]
- **Question to close it:** [...]

#### 🟡 SE-2 — [Short title]
...

#### 🟢 SE-n — [Short title]
...

### 4. Open Questions
| ID | Question | From |
|----|----------|------|
| Q1 | [B] [Feature_Name] ... | SE-1 |
| Q2 | [T] [Feature_Name] ... | SE-4 |

### 5. Wiki Conflicts
> Record only. Do not edit the wiki. The fix comes in the next ingestion cycle.

| Finding | Doc says | Wiki says | Wiki page |
|---------|----------|-----------|-----------|
| SE-3 | "..." | "..." | [[Project/wiki/concepts/...]] |

### 6. Resolution Log
| Finding | Decision | Note |
|---------|----------|------|
| SE-1 | FIX / CONTEST / QUESTION / ACCEPT RISK | [what changed and where, the reason for the contest, or the question ID] |
```
