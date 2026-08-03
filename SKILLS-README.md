# PM Skills Library

**Location:** E:\Mo'\.pm-skills\
**Source:** https://github.com/moamenmedhat14-lab/Product-Management-Skills
**Last synced:** 2026-07-09

## Available Skills

| Skill Folder | Skill Name | Version | Use When |
|-------------|-----------|---------|----------|
| `Feature-Analyzer/SKILL.md` | feature-analyzer | 2.2.0 | Analyzing a feature or epic → 5-phase process: Discovery → Research → Flow Ideation → Dependencies → RSD output, saved to `work-bench/[Analysis_Name]/analysis/` |
| `Strory-Writer/SKILL.md` | story-writer | — | Drafting development-ready tickets from wiki/ content (3-pass retrieval + publish to `work-bench/[Analysis_Name]/tickets/`) |
| `ai-pm-expert-analyzer/SKILL.md` | ai-feature-pm | 1.5.0 | Scoping or evaluating an AI feature → 11-phase PM playbook: opportunity framing through launch, monitoring, and 50 use cases, saved to `work-bench/[Analysis_Name]/analysis/` |
| `Workbench-to-raw/SKILL.md` | workbench-to-raw | 3.1.0 | Promoting an approved work-bench/ draft into raw/ (surgical Bash merge, RSD formatting, cleanup options) |
| `continuous-discovery-analysis/continuous-discovery-analysis.md` | continuous-discovery-analysis | 6.1 | Comparing your product wiki vs competitor wiki → structured competitive intelligence: gap analysis, flow differences, market trend radar, synthesis + saved to Discovery-Insights/. Uses Disc. Raw/ and Disc. Wiki/ inside the Continuous-Discovery/ folder to separate discovery content from the main project raw/ and wiki/. |
| `product-review/product-review.md` | product-review | 1.0 | Running a structured product review combining Nielsen's 10 Usability Heuristics + Logic Model analysis → iterative assessment saved to Product-Review/ |
| `azure-backlog-retrieval/azure-backlog-retrieval.md` | azure-backlog-retrieval | 2.0 | Pulling work items (Epics/Features/PBIs/Bugs/Enhancements) for a module from Azure DevOps → saved to Backlog/Backlog-Raw/, then synthesized into a business knowledge doc in Backlog/Backlog-Wiki/ |
| `Backlog Gap Analysis/backlog-gap-analysis.md` | backlog-gap-analysis | 1.0 | Comparing main project wiki/ vs Backlog/Backlog-Wiki/ for a module → surfaces missing requirements/gaps, approved additions go to raw/ for later ingestion |

## How to Update
```bash
cd "E:\Mo'\.pm-skills"
git pull origin main
```
Claude will re-read this file and update the table after each pull.

## Usage Rule
Never hardcode skill names in project CLAUDE.md files.
Always check this file first — the library grows over time.