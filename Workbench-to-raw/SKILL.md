---
name: workbench-to-raw
description: Manages feature analysis lifecycle. Features a conversational iteration loop, strict RSD formatting, and a lightning-fast, surgical Bash merge pipeline that manipulates large files via CLI without loading them entirely into memory.
allowed-tools: Read, Glob, Grep, Bash, GenerateText, WriteFile, AskUser
argument-hint: "feature name" | WBR-1 | WBR-2 | WBR-3
---

# Workbench-to-Raw Workflow Skill

This skill manages the transition of feature analysis from the `work-bench/` folder to the `raw/` folder. It prioritizes collaborative discussion for business logic, point-by-point review, and a tightly controlled promotion pipeline that STRICTLY enforces the RSD document style and gives the user granular control over file merging.

---

## 1. STRICT OUTPUT FORMAT — RSD Promotion Template

Whenever you generate an RSD draft (especially in WBR-3), you MUST use this exact structure. DO NOT use standard conversational paragraphs for requirements. DO NOT deviate from the headers below. 

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

---

## 2. The Re-iteration Loop (WBR-2)

When the user asks to re-iterate on an existing workbench file, you MUST follow this conversational sequence:

* **Phase 1: Analyze & Discuss:** Use `Read` to pull the specific `work-bench/[feature-name].md` file. Output a brief analysis of its current state so you and the user can discuss missing business points or enhancements.
* **Phase 2: Point-by-Point Display:** As new enhancements or business logic are discussed, explicitly display the proposed changes or new text to the user so they can review it clearly.
* **Phase 3: The Action Prompt:** After displaying a new point or enhancement, you MUST pause and ask the user:
    *"How would you like to proceed?*
    *[1] **Accept & Update**: Save these additions to the original `.md` file in the workbench.*
    *[2] **Continue Iterating**: Discuss further tweaks to these points.*
    *[3] **Promote to RSD**: Convert the finalized feature into the RSD style for publishing."*
* **Execution:** If [1] is selected, use `WriteFile` to safely update the workbench file. If [2], continue the conversation. If [3], immediately trigger the **WBR-3 Promotion Pipeline**.

---

## 3. The Promotion Pipeline (WBR-3)

When the user selects "Promote to RSD style", you MUST follow this sequence strictly:

* **Phase 1: Display RSD Draft:** Convert the entire feature strictly into the **RSD Promotion Template** outlined in Section 1. Output this RSD-styled text to the user in the chat. Do NOT write to any file yet.
* **Phase 2: The Routing Prompt:** After displaying the draft, pause and explicitly ask the user:
    *"Review the RSD draft above. What would you like to do?*
    *[A] **Discard**: Cancel this promotion and make no changes to the file system.*
    *[B] **Merge to Existing File**: Inject this feature into a specific file in the `raw/` folder.*
    *[C] **Create New File**: Save this as a brand new file in the `raw/` folder."*
    **CRITICAL: YOU MUST STOP HERE AND WAIT FOR THE USER'S REPLY.**

* **Phase 3: Interactive Execution & Cleanup:**
    * If **[A]**, acknowledge and stop.
    * If **[C]**, ask for the new file name, write the new formatted RSD text to `raw/`, and immediately delete (`rm`) the original `work-bench/` file.
    * If **[B]**, trigger the **Strict Decision Tree Merge Sub-Routine**:
        * **Step 1:** Use `Glob` or `Bash` (`ls raw/`) to find all files in the `raw/` directory. Display them and ask: *"Which file would you like to merge this into?"* **STOP AND WAIT FOR REPLY.**
        * **Step 2:** Once the file is selected, ask the user: *"How would you like to place this feature inside the file? Choose one:*
            *[1] **Top of the File***
            *[2] **Bottom of the File***
            *[3] **After a Specific Section***
            *[4] **Within an Existing Section***
            *[5] **Override an Existing Section***"
            **STOP AND WAIT FOR REPLY.**
        * **Step 3 (Branch logic):** * If the user chose **[1] or [2]**, execute the Smart Surgical Injection (see Section 4), and automatically delete (`rm`) the original `work-bench/` file. **STOP.**
            * If the user chose **[3], [4], or [5]**, use `Grep` to extract just the section headers (`grep -n "^#" "raw/filename"`) to quickly map the file without reading the whole thing. Display this list to the user and ask: *"Here are the current sections. **Which specific section** should I target for this action?"*
            **STOP AND WAIT FOR REPLY.**
        * **Step 4:** Once the user names the specific section, execute the Smart Surgical Injection (see Section 4) to inject/replace the text, and **immediately use `Bash` to delete (`rm`) the original `work-bench/` file.**

---

## 4. Constraints & Tool Rules

- **SMART SURGICAL INJECTION PROTOCOL (CRITICAL FOR LARGE FILES):** Do NOT load a massive master file into your memory and rewrite it via `WriteFile`. This is too slow and risks data loss. To inject text into large files smartly:
    1. Save your finalized RSD draft to a temporary file (e.g., `work-bench/temp_draft.md`).
    2. Use `Bash` utilities to surgically inject the temp file into the master document.
        * **Top:** `cat "work-bench/temp_draft.md" "raw/file.md" > "raw/temp.md" && mv "raw/temp.md" "raw/file.md"`
        * **Bottom:** `cat "work-bench/temp_draft.md" >> "raw/file.md"`
        * **After/Within Section:** Find the line number of the target section using `grep -n`, then use `sed -i` (e.g., `sed -i '42r work-bench/temp_draft.md' "raw/file.md"`) or an `awk` script to insert the temp file contents exactly where needed.
        * **Override Section:** Use `sed` or `awk` to delete the old section's line range, then insert the new temp file contents.
    3. Always delete `temp_draft.md` when the injection is complete.
- **FILE PATH & SPACES RULE:** Whenever using `Bash`, `Read`, `Grep`, or `WriteFile`, you MUST enclose the file path in double quotes to prevent errors with spaces in filenames (e.g., `cat "raw/NERA _ Admin Portal RSD.md"`).
- Do not edit `wiki/` files directly during these operations.   