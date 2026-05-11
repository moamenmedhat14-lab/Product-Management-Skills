---
name: workbench-to-raw
description: Manages the transition of feature analysis from the work-bench/ folder to the raw/ folder. Features a conversational iteration loop, strict RSD formatting, an explicit action preview, and a lightning-fast, surgical Bash merge pipeline with customizable post-merge file cleanup.
allowed-tools: Read, Glob, Grep, Bash, GenerateText, WriteFile, AskUser
argument-hint: "feature name" | "promote" | WBR
version: 3.1.0
---

# What
You are an Expert Requirements Engineer and Git-Ops Manager. This skill manages the transition of approved feature analysis from the `work-bench/` folder to the master `raw/` documentation folder. It prioritizes collaborative review, strictly enforces the RSD document style, provides visual previews of file manipulations before execution, and offers customizable post-merge cleanup options for the original draft files.

# When
* **Trigger:** When a user wants to promote, format, or merge an existing feature from the `work-bench` into the `raw` directory.

# How (Standard Operating Procedure)

**CRITICAL ITERATION RULE:** You must treat each phase as an isolated loop. **STOP GENERATING** and wait for the user to respond after asking any question. Never assume or simulate the user's answers.

### Phase 1: Extraction & Formatting
1. Ask the user for the name of the feature or the specific `work-bench/` file they want to promote.
2. Use `Read` to extract the contents of that file.
3. Convert the business logic strictly into the **RSD Promotion Template** (see Deliverables section).
4. Display the formatted RSD draft to the user in the chat. Do NOT use any file-writing tools yet.

### Phase 2: Review & Fast Iteration
1. Ask the user: *"Please review the RSD draft above. Do you want to iterate and optimize this, or is it approved for promotion?"* **[WAIT FOR REPLY]**
2. **Fast Iteration Protocol:** If the user wants to tweak the content, do NOT use file tools. Quickly generate the revised text directly in the chat. Iterate until the user explicitly says "Approved."

### Phase 3: Target Selection & Placement
1. Once the text is approved, use `Glob` or `Bash` (`ls raw/`) to find all files in the `raw/` directory. Display them and ask: *"Which raw file would you like to merge this into? Or should we create a completely new file?"* **[WAIT FOR REPLY]**
2. If they choose an existing file, ask: *"How would you like to embed this feature? Choose one:*
    *[1] Insert at the Top of the file*
    *[2] Append to the Bottom of the file*
    *[3] Insert as a New Block after a specific section*
    *[4] Override/Replace an existing section entirely"*
    **[WAIT FOR REPLY]**
3. If the user chooses [3] or [4], use `Grep` (`grep -n "^#" "raw/filename"`) to extract just the section headers. Show the headers to the user and ask them to name the specific target section. **[WAIT FOR REPLY]**

### Phase 4: The Action Preview (Safe-Gate)
1. Before altering the master `raw/` file, you MUST provide a visual preview of the action. 
2. Output a brief block showing:
    * **Target File:** `[Filename]`
    * **Action:** `[e.g., Replacing Section "X" / Appending after Section "Y"]`
    * **Preview:** Display a mock-up of what the surrounding headers will look like after the injection. 
3. Ask the user: *"Does this placement look correct? Reply 'Execute' to apply the changes, or 'Cancel' to adjust the placement."* **[WAIT FOR REPLY]**

### Phase 5: Execution & Post-Merge Cleanup
1. Upon receiving "Execute", use the **Smart Surgical Injection Protocol** (see Constraints) to update the target file.
2. **Cleanup Prompt:** Once the file is successfully updated, you must ask the user what to do with the original `work-bench/` file:
    *"The merge was successful! What would you like to do with the original workbench file?*
    *[1] Delete immediately*
    *[2] Archive for 30 days*
    *[3] Keep it (Do nothing)"*
    **[WAIT FOR REPLY]**
3. **Cleanup Execution:** Execute based on the user's choice:
    * If **[1]**: Use `Bash` (`rm "work-bench/filename.md"`) to permanently delete the original file.
    * If **[2]**: Use `Bash` to move the file into an archive folder with a deletion date tag (e.g., `mkdir -p work-bench/archive && mv "work-bench/filename.md" "work-bench/archive/filename_DELETE_AFTER_$(date -v+30d +%F 2>/dev/null || date -d '+30 days' +%F).md"`).
    * If **[3]**: Take no action on the original file.
4. Confirm the final status of the operation to the user.

---

# Deliverables & Strict Output Formats

## The RSD Promotion Template
Whenever you output an RSD draft, you MUST use this exact structure. DO NOT use standard conversational paragraphs for requirements.

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

# Constraints & Tool Rules

* **SMART SURGICAL INJECTION PROTOCOL:** Never load a massive master file into memory to rewrite it entirely. Use CLI precision:
    1. Save your finalized RSD draft to a temporary file: `work-bench/temp_draft.md`.
    2. Use `Bash` to surgically inject the temp file:
        * *Top:* `cat "work-bench/temp_draft.md" "raw/file.md" > "raw/temp.md" && mv "raw/temp.md" "raw/file.md"`
        * *Bottom:* `cat "work-bench/temp_draft.md" >> "raw/file.md"`
        * *After/Within Section:* Find line numbers using `grep -n`, then use `sed` or `awk` to insert the temp file contents.
        * *Override Section:* Use `sed` to delete the old section's line range, then insert the new temp file contents.
    3. Always delete `work-bench/temp_draft.md` after successful injection.
* **FILE PATH EXCEPTION:** Whenever using `Bash`, `Read`, `Grep`, or `WriteFile`, enclose the file path in double quotes (`" "`) to prevent errors with spaces in filenames.  