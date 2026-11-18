---
name: second-eye
description: Fresh-context business review of any analysis (FAD, Discovery, RSD, PRD, ticket set, gap file). Based on the Elephant-Goldfish Model (github.com/google/elephant-goldfish-model), adapted from software design to business concepts and logic. Spawns stateless "Goldfish" reviewer subagents that read only the document and the project wiki, with no chat history, to find gaps, edge cases, contradictions, and unstated assumptions. Runs ONLY when Moamen explicitly asks for a "second eye".
version: 1.0.0
disable-model-invocation: true
---

# Second Eye

## What
Second Eye gives an analysis a review by readers who did not take part in writing it.

The idea comes from the Elephant-Goldfish Model:

| Role | Context | Job |
|------|---------|-----|
| **Elephant** (you, the main agent) | The full session, the project, earlier decisions | Collects inputs, sends reviewers, merges findings, helps resolve them |
| **Goldfish** (fresh subagents) | The target document and the project wiki only. No chat history. | Each one finds gaps through one business lens |
| **Moamen** | Business goals and constraints | Decides which findings to fix, contest, or accept |

The author of an analysis fills holes with knowledge that is in their head but not on the page. A Goldfish does not have that knowledge. So a Goldfish sees the holes.

## When
* **Trigger (explicit only):** Moamen asks for a second eye. Examples:
  - "Give me a second eye on [analysis / file / topic]"
  - "Second eye on this"
  - "Run second eye on the edge cases of [topic]"
* **Never run this skill on your own.** Do not start it as a step of another skill. Do not start it because an analysis looks finished.
* **Target:** any analysis document. Examples: `Analysis [X].md`, `Discovery [X].md`, a FAD, an RSD section, a `[Module]_Gaps.md` file, a ticket file or a ticket folder, a PRD.

## Execution Rules (CRITICAL)

1. **Fresh context is the product.** Every reviewer MUST run in a new subagent with no conversation history. Use the `Agent` tool with `subagent_type: "general-purpose"`. **Never use `subagent_type: "fork"`.** A fork inherits the chat, so it is not a Goldfish.
2. **If you cannot spawn subagents, stop.** Tell Moamen: "I cannot start fresh reviewers in this session. A review in my own context is not a second eye." Do not do the review yourself as a substitute.
3. **Pass prompts verbatim.** Build each reviewer prompt from `references/goldfish-lenses.md`: the Core Mandates, plus the lens section, plus the Inputs block. Do not add your summary of the analysis. Do not add decisions from the chat. Do not add your opinion. If the document does not say it, the reviewer must not know it.
4. **Reviewers are read-only.** They do not write files. They do not call Slack, Azure DevOps, Figma, Drive, or the web. Only you (the Elephant) write the report.
5. **Stress-test, do not agree.** No praise ("great", "solid", "well thought out"). Lead with what is wrong or missing. Do not apologize when Moamen corrects you. Give the corrected logic.
6. **Do not filter the Goldfish.** When you merge findings, do not delete or soften a finding because you think the doc covers it. You contest a finding only in the Resolve step, with a quote as evidence.
7. **Artifact first.** Write the full report to a file. In chat, give only the path, the counts, and the top blockers.
8. **Writing style.** Write the report and all chat replies in Simplified Technical English (pragmatic mode): short sentences, active voice, no "should/may/might/could", no semicolons, no filler words.

## How

### Step 1 — Intake
1. **Active context.** Use the active project from the session (for example `Team-Smart/`). If you do not know it, ask.
2. **Target.** Use the file that Moamen names, links, or has open. If the target is not clear, ask for it in one plain-text question and stop.
3. **Scope.** The default is the full file. If Moamen names a section ("second eye on the approval flow"), set the scope to that section. Reviewers still read the full file for context.
4. **Topic name.** Use the work-bench topic folder name of the target (for example `work-bench/Shift-Swap/analysis/...` → `Shift-Swap`). If the target is not in `work-bench/`, use the file name without the extension.
5. **Baseline (Goldfish context).** Collect these files and give them as paths:
   - `[Project]/wiki/index.md`. The reviewers use it to find related wiki pages. They can read any page under `[Project]/wiki/`.
   - Every file the target links to (wikilinks or markdown links) that is inside the active project folder and exists on disk.
   - If a link points outside the project folder or to a URL, list those links and ask Moamen which to include.
   - **Never include `raw/`**, `Daily Notes/`, or another project's folder, unless Moamen asks for it.
6. **AI features only.** If the analysis is about an AI feature, query the `AI PM Brain` notebook (per the root `CLAUDE.md`) about the relevant risks: errors, trust, feedback, user control, and fallback. Add the answer text to the Inputs block under the label `[AI PM Brain] context`. Turn on the optional AI checklist in the Edge-Case Hunter lens.
7. **Lenses.** By default, run all 7 lenses (below). If Moamen names a focus ("only edge cases", "only permissions"), run those lenses **plus Naive Reader**. Naive Reader always runs, because its paraphrase is the base check.
8. **Mode.** The default is **Consolidated**: all lenses in parallel, one report, no stops. If Moamen says "step by step" or "interactive", use **Interactive** mode: run Naive Reader first, show its paraphrase, and wait for him. Then run the other lenses.

Do not ask more questions than these. If the target is clear, start without questions.

### Step 2 — Dispatch the Goldfish
Send all the selected lenses in **one message, in parallel**, one `Agent` call per lens:

| # | Lens ID | Persona | Looks for |
|---|---------|---------|-----------|
| 1 | `naive-reader` | New team member | Paraphrase, undefined terms, assumed knowledge, vague phrases |
| 2 | `logic-critic` | Business analyst / auditor | Contradicting rules, missing "else" branches, conditions without outcomes, calculations, conflicts with the wiki |
| 3 | `edge-case-hunter` | QA lead | Boundaries, time, concurrency, order, missing data, cancellations, volume |
| 4 | `actor-permission` | Operations manager | Who can do, see, approve, and is notified. Absent actors. Role changes. |
| 5 | `lifecycle-state` | Process owner | States, transitions, undo/edit, history, downstream effects, existing data at launch |
| 6 | `zoom-out` | Skeptical product lead | Root problem, value, success metric, over-scope, overlap with existing features, false compromises |
| 7 | `dev-qa-readiness` | Developer + tester | Decisions left to the developer, untestable rules, missing field rules, messages, and empty states |

Each prompt = **Core Mandates** + **the lens section** + **Inputs block** (format in `references/goldfish-lenses.md`).

Wait for all reports. If a reviewer fails, times out, or returns nothing, retry it once. If it fails again, mark it `not run`. Never count a failed reviewer as "no findings".

### Step 3 — Merge (Synthesizer)
1. **Deduplicate.** If two or more lenses report the same problem, merge them into one finding. Keep the highest severity. List all lenses under "Raised by".
2. **Normalize the location.** Use the section heading (and the line numbers if the reviewer gave them).
3. **Number the findings** `SE-1`, `SE-2`, ... in severity order: 🔴 Blocker → 🟡 Major → 🟢 Nit.
4. **Collect open questions.** If a finding needs an answer from a stakeholder, add it to the Open Questions section. Tag it `[B]` (business) or `[T]` (technical), followed by `[Feature_Name]`. This is the same format as Moamen's Slack questions.
5. **Collect wiki conflicts.** If a finding says that the doc contradicts the wiki, also list it under Wiki Conflicts. Do not edit the wiki.
6. **Write the report** with the template in `references/report-template.md`:
   - Path: `[Project]/work-bench/[Topic]/second-eye/Second Eye [Topic].md`.
   - If the file exists, add a new `## Round N — YYYY-MM-DD` section at the bottom. Do not change earlier rounds.
   - The frontmatter is `status: Draft`. Never set `Done` yourself.
   - Get the date with `date`. Do not guess it.

### Step 4 — Report back
In chat, give only:
- The link to the report: `[[...Second Eye [Topic].md]]`.
- The counts: `🔴 X · 🟡 Y · 🟢 Z`, and the reviewers marked `not run`, if any.
- One line for each Blocker (ID + short title).
- A note if the Naive Reader paraphrase does not match the intent of the doc. A wrong paraphrase is a finding in itself.

Then ask one question with these options:
1. Resolve findings with me
2. I will update the doc myself
3. Stop here

### Step 5 — Resolve (only if Moamen chooses it)
You now work as the Elephant. You use the full session context.
1. Ask which findings to resolve: all of them, the Blockers + Majors, or a list of IDs.
2. Classify each selected finding:
   - **FIX:** the doc has a real gap. Write the exact new text and the place it goes in the doc.
   - **CONTEST:** the doc already covers it, or the finding is wrong. Quote the lines that cover it.
   - **QUESTION:** only a stakeholder can answer it. Give the `[B]`/`[T]` question text.
   - **ACCEPT RISK:** a known gap that is out of scope for now. Give the reason.
3. **Show the full plan before you change anything.** For each item, show the file, the place, and the exact new text. Wait for approval.
4. Apply only the approved edits, and only to the target doc:
   - Read the target again before you edit it. If the file changed on disk, those edits are Moamen's and they are intentional. Keep them.
   - **Never edit `wiki/` pages.** Record wiki conflicts in the report. They get fixed in the next ingestion cycle.
   - If the target is a ticket, also reflect the change in the topic's main analysis doc in `work-bench/[Topic]/analysis/`. If the wording conflicts, ask which wording wins.
   - Use Moamen's writing style for the doc type (RSD style from Feature-Analyzer Phase 5, ticket style from Strory-Writer).
5. For QUESTION items, offer to add them to `work-bench/[Topic]/analysis/business-questions.md`. Do not send anything to Slack unless Moamen asks. If he asks, show the message first and wait for approval.
6. Fill in the **Resolution Log** table of the current round in the report.

### Step 6 — Re-check (optional, max 1)
After the fixes, Moamen can ask for a re-check.
- Run a new round. Send only the lenses that raised Blocker or Major findings, plus Naive Reader. Scope them to the changed sections.
- Tell them nothing about the earlier round. They stay fresh. You compare the results with the earlier round.
- Each session allows **one** re-check. After that, record the remaining findings as `ACCEPT RISK` in the Resolution Log. A new full second eye runs only if Moamen asks for it.

## Files in this skill
- `SKILL.md`: this orchestrator.
- `references/goldfish-lenses.md`: the Core Mandates, the 7 lens prompts, and the Inputs block format. Pass them verbatim.
- `references/report-template.md`: the report layout.

## Credit
The workflow is adapted from the Elephant-Goldfish Model by Google (Apache-2.0): https://github.com/google/elephant-goldfish-model. This skill changes the reviewer lenses from software design to business concepts and logic.
