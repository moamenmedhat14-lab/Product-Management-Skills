---
name: workbench-to-raw
description: Manages the lifecycle of feature analysis from the workbench to the source documents (raw/). Supports creation, iteration, and promotion/merging.
---

# Workbench-to-Raw Workflow Skill

This skill manages the transition of feature analysis from the `work-bench/` folder to the `raw/` folder. It ensures that analysis is iterated upon in a temporary workspace before being promoted to the "Source of Truth".

## Principles
1. **Workbench First**: All new feature analysis starts in `work-bench/`.
2. **Iterative Design**: Use the workbench to refine details with the user.
3. **Promotion**: Once finalized, the analysis is moved or merged into `raw/`.
4. **Automatic Cleanup**: Files are removed from `work-bench/` immediately after promotion.

## Operations

### WBR-1: Create Analysis
- **Intent**: Start analysis for a new feature.
- **Action**: 
    1. Analyze the requirements/topic.
    2. Create `work-bench/[feature-name].md`.
    3. Write detailed analysis (goals, phases, logic, implementation notes).
- **Format**: Use a structured markdown format (like `# Feature Name`, `## 🎯 Feature Goal`, `## 🟢 Phase 1`, etc.).

### WBR-2: Update Analysis
- **Intent**: Re-iterate on existing workbench analysis.
- **Action**:
    1. Read the existing `work-bench/[feature-name].md`.
    2. Apply updates based on new instructions or feedback.
    3. Overwrite the file with the updated content.

### WBR-3: Promote to New File
- **Intent**: Finalize a standalone feature and move it to `raw/`.
- **Action**:
    1. Copy `work-bench/[feature-name].md` to `raw/[feature-name].md`.
    2. Delete `work-bench/[feature-name].md`.
    3. Report the successful promotion.

### WBR-4: Merge to RSD
- **Intent**: Finalize a feature and append it to the main Requirements Specification Document (RSD).
- **Action**:
    1. Identify the target RSD in `raw/` (e.g., `raw/NERA _ Admin Portal RSD.md`).
    2. Read `work-bench/[feature-name].md` content.
    3. Append the content to the end of the target RSD.
    4. Delete `work-bench/[feature-name].md`.

## Constraints
- Do not edit `wiki/` files directly during these operations.
- Ensure `raw/` documents remain the "Ground Truth".
- Always delete the workbench file after promotion/merge to maintain a clean workspace.
