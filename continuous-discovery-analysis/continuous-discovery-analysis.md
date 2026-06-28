# Skill: Continuous Discovery Analysis

**Skill ID:** continuous-discovery-analysis
**Version:** 1.1
**Triggers:** "run discovery analysis", "compare with competitors",
              "discovery insight", "what are competitors doing"

---

## Purpose
Compare the project's own product wiki against the Continuous Discovery
competitor wiki to produce structured competitive intelligence.
Output is always saved to Continuous-Discovery/Discovery-Insights/ with full iteration support.

---

## Inputs (Claude reads these before starting)

### Source 1 — Your Product Wiki
Path: `[project-root]/wiki/`
Read: `wiki/index.md` first, then drill into `wiki/features/` and `wiki/user-flows/`
Extract: every epic, feature, and user flow your product has

### Source 2 — Competitor Wiki
Path: `[project-root]/Continuous-Discovery/wiki/`
Read: `Continuous-Discovery/wiki/index.md` first, then drill into
`competitors/`, `features/`, `flows/`, `market-trends/`
Extract: every competitor feature, flow, and trend documented there

### Source 3 — Existing Insights (for iteration)
Path: `[project-root]/Continuous-Discovery/Discovery-Insights/`
Read: `Continuous-Discovery/Discovery-Insights/INSIGHTS-INDEX.md`
If iterating on a specific date: read all files in `Continuous-Discovery/Discovery-Insights/[date]/`

---

## Analysis Framework

Run all four analyses in sequence. Do not skip any.

### Analysis 1 — Gap Analysis (What They Have, You Don't)
For every feature/epic found in Continuous-Discovery/wiki/ that has NO
corresponding entry in the product wiki/:

Output format per gap:
```
### [Feature Name]
- **Competitor(s):** [who has it]
- **Their implementation:** [brief description]
- **Estimated user impact:** [why users might prefer it]
- **Gap severity:** Critical | High | Medium | Low
  - Critical = core to the product category, you're missing it entirely
  - High = common across 2+ competitors
  - Medium = one competitor, niche use case
  - Low = edge case or experimental feature
- **Recommended action:** Build | Monitor | Ignore | Differentiate
```

### Analysis 2 — Flow Difference Analysis (Same Feature, Different Edge)
For every feature that EXISTS in both wikis but with different flows or approaches:

Output format per difference:
```
### [Feature Name]
- **Your current flow:** [summary from your wiki]
- **Competitor approach:** [who does it differently and how]
- **Competitive edge they have:** [what's better about their version]
- **Your potential response:** Adopt | Adapt | Differentiate | Defend
```

### Analysis 3 — Market Trend Radar
From Continuous-Discovery/wiki/market-trends/, extract all documented trends.
For each trend:

Output format:
```
### [Trend Name]
- **Trend description:** [what's happening in the market]
- **Who's leading it:** [competitors driving this trend]
- **Your current position:** Ahead | On-par | Behind | Not started
- **Gap to close:** [what you'd need to do to be on-par or ahead]
- **Urgency:** Now | Next Quarter | Next Year | Watch
```

### Analysis 4 — Synthesis & Priorities
After running all three analyses above, synthesize:
1. Top 3 critical gaps to address immediately
2. Top 3 flow improvements worth fast-tracking
3. One trend you're furthest behind on
4. One area where you're actually ahead of the market

---

## Output Protocol

### Step 1 — Determine Output Folder

The output folder is ALWAYS inside Continuous-Discovery/, never at the project
root or vault root. The correct path is:

`[project-root]/Continuous-Discovery/Discovery-Insights/`

Never create Discovery-Insights/ anywhere else.

Check if I specified a date to iterate on:
- If yes → use `Continuous-Discovery/Discovery-Insights/[that date]/`
- If no (new run) → use today's date: `Continuous-Discovery/Discovery-Insights/YYYY-MM-DD/`

Create the folder if it doesn't exist.

### Step 2 — Write the Four Output Files

**File 1: `insight-report.md`** — full combined report
```yaml
---
title: Discovery Insight Report
generated: YYYY-MM-DD
iteration: 1        ← increment on each iteration of the same date
product-wiki-snapshot: [date of last update to wiki/log.md]
discovery-wiki-snapshot: [date of last update to Continuous-Discovery/wiki/log.md]
---
```
Content: all four analyses in full, in order.

**File 2: `gap-analysis.md`** — Analysis 1 output only, standalone
```yaml
---
title: Gap Analysis
generated: YYYY-MM-DD
iteration: 1
total-gaps: [number]
critical-gaps: [number]
---
```

**File 3: `flow-differences.md`** — Analysis 2 output only, standalone
```yaml
---
title: Flow Difference Analysis
generated: YYYY-MM-DD
iteration: 1
total-differences: [number]
---
```

**File 4: `trend-radar.md`** — Analysis 3 + 4 output, standalone
```yaml
---
title: Market Trend Radar
generated: YYYY-MM-DD
iteration: 1
total-trends: [number]
behind-on: [number]
ahead-on: [number]
---
```

### Step 3 — Write or Update `iteration-log.md`

Always append to this file, never overwrite:
```markdown
## Iteration [N] — [YYYY-MM-DD HH:MM]
- **Triggered by:** [what I asked / what changed]
- **What changed from previous iteration:**
  - New gaps found: [list or "none"]
  - Gaps closed: [list or "none"]
  - Trends updated: [list or "none"]
  - Flow differences revised: [list or "none"]
- **Input changes since last run:**
  - Product wiki updated: [yes/no — what changed]
  - Discovery wiki updated: [yes/no — what changed]
```

### Step 4 — Update `Continuous-Discovery/Discovery-Insights/INSIGHTS-INDEX.md`

Always update this master index after every run or iteration:
```markdown
# Discovery Insights Index

| Date | Iteration | Critical Gaps | Trends Behind | Report |
|------|-----------|--------------|---------------|--------|
| 2026-06-28 | 3 | 4 | 2 | [[2026-06-28/insight-report]] |
| 2026-06-15 | 1 | 7 | 5 | [[2026-06-15/insight-report]] |
```

### Step 5 — Log in Continuous-Discovery/wiki/log.md
Append:
```
## [YYYY-MM-DD] discovery-analysis | Iteration [N] | [N] gaps, [N] trends
```

---

## Iteration Rules

### Running a Fresh Analysis (no date specified)
→ Create new `Continuous-Discovery/Discovery-Insights/YYYY-MM-DD/` folder with iteration: 1

### Iterating on an Existing Date
Triggers: "iterate on [date]", "update the [date] insights", "revise [date]"
→ Read all existing files in `Continuous-Discovery/Discovery-Insights/[date]/`
→ Re-run only the analyses where inputs changed
→ Increment iteration number in all frontmatter
→ Append to iteration-log.md, never overwrite
→ Update INSIGHTS-INDEX.md

### Partial Iteration (one analysis only)
Triggers: "re-run gap analysis for [date]", "update just the trend radar"
→ Re-run only the requested analysis
→ Update only that file + insight-report.md + iteration-log.md
→ Mark other files as unchanged in iteration-log.md

### Comparing Two Dates
Triggers: "compare [date1] insights with [date2]"
→ Read both folders
→ Show what improved, what regressed, what's new
→ Do NOT create a new insights folder for this — output to chat only

---

## Ingest Workflow for Continuous-Discovery/raw/

When I drop a new competitor source in Continuous-Discovery/raw/:
1. Read it fully
2. Identify: which competitor, which features, which flows, any trend signals
3. Update or create pages in Continuous-Discovery/wiki/competitors/
4. Update or create pages in Continuous-Discovery/wiki/features/
5. Update or create pages in Continuous-Discovery/wiki/market-trends/ if relevant
6. Update Continuous-Discovery/wiki/index.md
7. Append to Continuous-Discovery/wiki/log.md:
   `## [YYYY-MM-DD] ingest | [Source: Competitor Name — what it covers]`
8. Ask: "Discovery wiki updated. Want to run a new analysis or iterate on an existing one?"