---
name: feature-analyzer
description: Systemic analysis and feature breakdown skill — performs structured retrieval from internal documentation, analyzes existing features or drafts completely new ones, seeks user approval for web research, and uses an iterative drafting process to output finalized requirements into a workbench directory.
allowed-tools: Read, Glob, Grep, Bash, GenerateText, SearchWeb, ScrapeWeb, WriteFile, AskUser
argument-hint: "epic name" | "feature topic" | "analysis" | "new feature" | ANA-1 | FEAT-1
---

# LLM Epic & Feature Analyzer Skill

## Systemic Feature Breakdown & Impact Analysis

This skill analyzes project documentation to deconstruct high-level epics and features into rigorous, systemic breakdowns. It handles both existing documentation analysis and the drafting of completely new features. It is equipped to research industry standards via the web ONLY upon user confirmation, following a strict iterative drafting process before saving finalized outputs.

---

## 1. EXTERNAL RESEARCH RULE — Benchmarking, Gaps & New Features

When internal documentation has gaps, lacks technical implementation details, or when you are tasked with a **completely new feature**, you must **seek user confirmation** before searching the web.
* **Identify Gaps / New Features:** First, attempt to complete your analysis using internal documentation. If the topic is a new feature (no internal docs found) or has missing logic, note this.
* **The Permission Trigger:** Use the `AskUser` tool (or ask in chat) with a specific prompt:
  * *For gaps:* "I found a gap in the documentation regarding [Specific Topic]. Would you like me to search the web for industry-standard solutions to fill this gap?"
  * *For new features:* "I couldn't find internal documentation for this new feature. Would you like me to research industry standards and propose a baseline RSD draft for [Feature Name]?"
* **Execution:** Only use `SearchWeb` and `ScrapeWeb` if the user explicitly says "Yes" or approves the search.
* **Attribution:** Clearly distinguish between internal project requirements and external web suggestions. Label external findings under a "Proposed Industry Solutions / Benchmarks" sub-heading.
* **Relevance:** Only scrape technical documentation, product teardowns, or verified development forums.

---

## 2. WORKFLOW & ITERATION RULE — The Workbench Pipeline

You must strictly follow this iterative loop for all feature analyses and new feature drafts:

* **Step 1: The Draft.** Generate the initial feature breakdown using the standard RSD template. Output this directly to the user in the chat interface. Do NOT write to the file system yet.
* **Step 2: The Review.** Pause and explicitly use the `AskUser` tool (or prompt the user in chat) to ask for feedback, revisions, or approval on the generated draft.
* **Step 3: The Iteration.** Apply any user feedback to refine the draft. Repeat Step 1 and Step 2 until the user explicitly states the draft is "Accepted" or "Approved".
* **Step 4: The Publish.** Once accepted, use `Bash` or `WriteFile` to save the final markdown document into the `work-bench/` directory. Name the file logically (e.g., `work-bench/FEAT-new-payment-gateway.md`).

---

## 3. SEARCH SOURCE RULE — Internal Documentation

* For existing features, derive all base flows, system reflections, and core dependencies ONLY from the designated project documentation folder (e.g., `wiki/`).
* Every internally impacted area MUST be traced back to a specific requirement ID (e.g., R.S#2.1) found in the documentation.

---

## 4. Output Style — RSD Structure & Template

The analysis output must follow the standard Requirements Specification Document (RSD) structural style. Use the following template as your strict writing guide:

**[Module Number]. [Module Name] Management:**
*Contextual description of the feature or module.*

**R.S#[ID] | [Feature Action Name] (e.g., Create New Unit)**
* **Trigger / Input Fields:** State inputs clearly with types, e.g., "Name (mandatory, free-text)". Mark optional fields as "[Optional]".
* **Validations:** List specific constraints, uniqueness rules, and block conditions.
* **Upon Action:**
    * **User Feedback:** (e.g., Success messages, confirmation pop-ups)
    * **System Reflection:** (e.g., Entity added to list, status changed)
    * **Routing:** (e.g., Redirected to details screen)
    * **Logging:** (e.g., Action, user, and timestamp logged)
* **Benchmarking / Web Solutions:** (Include only if external research was authorized and utilized to draft new features or solve gaps).

## Operation Modes

### ANA-1 / "analysis": Systemic Analysis
- Gather all relevant documentation pages and requirement IDs for existing features.
- Identify gaps and run the Permission Trigger for web research if needed.
- Output findings strictly using the RSD Structure and initiate the Workbench Pipeline (Draft -> Review -> Publish).

### FEAT-1 / "new feature": Feature Draft & Breakdown
- Check internal docs. If missing (new feature), run the Permission Trigger to research and establish a baseline.
- Provide a structured feature analysis using the RSD Template.
- Initiate the Workbench Pipeline (Draft -> Review -> Publish).