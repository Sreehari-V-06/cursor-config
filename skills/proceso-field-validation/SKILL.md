---
name: proceso-field-validation
description: >
  Runs process field validation only when that process already has a knowledge
  base at .cursor/knowledge/procesos/<number>/legacy.md. Order: common legacy
  architecture, then that process file, then a cross-check against the migrated
  frontend, then a report of missing, incomplete, and incorrect checks. After
  the report, the user marks Fix on the rows to change. Use when the user
  invokes /proceso-field-validation or asks to validate again after fixes.
  Do not use this skill to build the process knowledge base or to edit the form.
  The form fix is /proceso-field-fix.
disable-model-invocation: true
---

# Process field validation

This skill runs only when the open folder contains the trees below. The folder name does not matter. Check that before asking questions or reading code. If any path is missing, stop. Name each missing path. Tell the user to put these next to each other in the open folder. Do not search outside that folder.

```
<open folder>/
  .cursor/
  legacy/vuc_mtto_aga2/
  legacy/vuc_mtto_commons/
  Project/frontend/
```

Run these four steps in order. Do not cross-check, and do not write the report, until steps 1 and 2 are done. This pass does not edit code. A form fix is a separate command, `/proceso-field-fix`.

1. **Common knowledge.** Read `.cursor/knowledge/legacy-base/architecture.md`. If that file is missing, stop and say so. Use it for the platform contract (`class="required"` versus a label asterisk, stripes, `parentSelectName`, jquery `ignore: []`). Do not treat a process-script comment as that contract.
2. **Process knowledge.** Read `.cursor/knowledge/procesos/<processNumber>/legacy.md`. This skill runs only when that file is already present. If it is missing, stop. Tell the user this skill is not allowed for that process until the knowledge base exists, and to build it with `/legacy-proceso-knowledge-base` (process number and old-code controller). Do not build `legacy.md` in this skill. Do not invent the legacy inventory.
3. **Cross-check.** Compare that legacy baseline with the migrated tree, field by field and behavior by behavior.
4. **Report.** Write `model-result/<processNumber>/filed-validation/report.md`.

## Ask first

Ask for the process number only. Do not ask for the old-code controller.

1. If the user has not given a **process number**, ask for it and stop.
2. As soon as the process number is known, check that `.cursor/knowledge/procesos/<processNumber>/legacy.md` exists. If it does not, stop. This skill is not allowed without that knowledge base. Do not offer to build it here.

The migrated tree is optional. Search `Project/frontend/**/tramites/<processNumber>` when the user does not give a path. If that search returns more than one folder, list the paths and ask which tree to use. If it returns none, ask for the path.

Confirm the migrated tree is this process: the folder name, a route, or a procedure id constant equals the process number. If it does not, stop and report both values.

## Write

`model-result/<processNumber>/filed-validation/report.md` at the repository root.

Fill [report-template.md](report-template.md). Delete none of its sections. Do not write this report under `.cursor/knowledge`.

Write for a person who will choose rows to fix, and for the frontend developer who will make the change. Open with what they can still do on the screen. Under each section heading, one short sentence in everyday language. In the reading columns, say **Old page** and **New page**. The **Item** cell is the full label shown on the screen. Do not shorten it. The last column is **Where to change**: the file inside this process folder, the field or method, and the change in plain words. Name a shared file only when this form cannot do the change alone. Do not fill that column with a `legacy.md` citation. Group **Incorrect** rows under a short heading when several rows are the same kind of problem. Keep every row.

The first column of **Incorrect**, **Missing**, **Incomplete**, and **UI behavior** is **Fix**. An open row uses `- [ ]`. A chosen row has `x` inside the brackets: `- [x]`, `- [X]`, `- [ x]`, or `[x]`. A Match row leaves that cell empty. An `x` written outside the brackets is not a choice. This mark is plain Markdown so the same file works on any system. Do not use an HTML checkbox, a preview widget, or a plugin. Do not add a second list above the table. When this pass rewrites the report, keep a chosen mark on a row that is still open. Do not edit the form in this pass. The user runs `/proceso-field-fix` after saving the marks.

If `model-result/<processNumber>/filed-validation/report.md` already exists, this pass updates that file. Do not start a second report. Read the previous **Incorrect**, **Missing**, **Incomplete**, and **Corrected** rows first. Re-check every one of those items in the current migrated tree, then rewrite the file with this pass’s results.

## Steps

```
- [ ] Stop unless the open folder contains .cursor, legacy/vuc_mtto_aga2, legacy/vuc_mtto_commons, and Project/frontend
- [ ] Ask for the process number when the message does not include one
- [ ] Stop if .cursor/knowledge/procesos/<processNumber>/legacy.md is missing
- [ ] Read .cursor/knowledge/legacy-base/architecture.md
- [ ] Read .cursor/knowledge/procesos/<processNumber>/legacy.md
- [ ] Read model-result/<processNumber>/filed-validation/report.md when it exists
- [ ] Confirm the migrated tree is this process number
- [ ] Map each legacy field to a migrated control
- [ ] Cross-check validation and behavior against the migrated code
- [ ] Classify each finding, including previously reported items
- [ ] Update model-result/<processNumber>/filed-validation/report.md
- [ ] Tell the user to mark Fix with `- [x]`, save, and run /proceso-field-fix, then stop
```

### Map fields

Match a legacy **Fields** row to a migrated control by label, then by the posted name or model path. Search the trámite folder for `validadores`, `Validators`, `inputFilter`, `required`, and the method that blocks Continuar or Guardar.

A legacy field with no migrated control is **Missing**. A migrated control with no legacy row is listed under **Only in the migrated form**. Do not call that a legacy gap.

Skip **Dead selectors** as migrated gaps when the legacy file says the id is not in the capture page. List them under **Correctly absent**.

### Compare each field

Compare the legacy columns to the migrated control. Quote both patterns when they differ. Give one concrete value that one side accepts and the other rejects before calling the row **Incorrect**.

Check these, and record a finding only where they differ:

- Required when, including a role or visibility condition
- Shown when and hidden when. If a branch shows or hides a container, list every input inside that container. A field that is on screen only for one role is not the same as a field that is always rendered, even when both are required for the shown role
- Enabled when and disabled when. A field that stays on screen but cannot be used is not a hidden field
- Readonly when. A readonly field that a calendar or another control still fills is not a disabled field, and it is not a field the user can type
- Headings and other text. If a heading, label, or message is inside a shown or hidden container, list that text with the fields. If the wording changes with a role, tab, or other condition, record both wordings. A heading that stays on screen with the same words is not a hidden heading
- Put each shown, hidden, enabled, disabled, or readonly field, and each heading or text, in the UI behavior section. One row per item and per behavior. Set **Kind** to `Text` for a heading, label, or message, and `Field` for an input or other control. `Always` when that state does not change. Do not treat “required when a role is selected” as a substitute for shown, hidden, enabled, disabled, readonly, or a heading. If that same mismatch is already in Incorrect, Missing, or Incomplete, do not count it a second time
- Max length
- Character filter (keypress, keyup, or `inputFilter`)
- Validator regex, including a regex that does not block submit
- Message
- On failure: clear the field, mark the control invalid, or alert only
- Server check (lookup, date parse) and whether failure blocks save
- Catalog parent: `parentSelectName` versus lists loaded independently

Also compare **Role branches**, **Submit validation**, and **Documents** from `legacy.md`. A submit path that never runs the migrated validators is **Incorrect** even when each control has a validator.

### Classify

Use one verdict per finding.

- **Incorrect** — a value the legacy rule accepts is rejected, a value the legacy rule rejects is accepted, or the check does not block the action it guards (a modal that leaves the control valid, a filter that rejects every typed character).
- **Incomplete** — part of the legacy rule is present and the blocking outcome for the same values still matches. The absent part is a message, a clear-on-error, a maxlength that is also enforced by the pattern, or a catalog label. Use **Incorrect** instead when a user can submit a value or a combination the legacy rule rejected.
- **Missing** — the legacy rule has no migrated check, or the control is absent.

**Match** — same blocking outcome. List matches in the counts and the match table. Do not also file them as findings.

A legacy cell that says `Not found` is not a gap. Put it under **Not compared**.

### Update after a fix

When the previous report exists, pair each previous **Incorrect**, **Missing**, and **Incomplete** item with this pass by its item name.

- The item now matches the legacy rule: remove it from the open table. Add it under **Corrected** with the previous verdict, the previous mismatch, and the rule the current code enforces. Cite the migrated file that now holds the fix.
- The item is still wrong: keep it in **Incorrect**, **Missing**, or **Incomplete** with this pass’s verdict. If the verdict changed, say the previous verdict in **Where to change**.
- A **Corrected** row that fails again: remove it from **Corrected** and put it back in the open table. In **Where to change**, say it had been corrected and failed again.
- A new mismatch that was not in the previous report: add it to the open table for this pass’s verdict.

**Corrected** is a record of what changed. It is not an open gap. Do not count a corrected item again under Match. Increment the pass number and set the run date.

## Where to change

Every finding names the file inside this process folder, the field or method, and the change. Do not grade a sibling trámite. Do not treat a label asterisk alone as a legacy required rule; `legacy.md` already applied that distinction.

## After writing

Tell the user the path `model-result/<processNumber>/filed-validation/report.md` and the counts: match, corrected, incorrect, incomplete, missing. Lead with **Corrected** when this pass closed a previous finding, then **Incorrect**, **Missing**, and **Incomplete**.

Then stop. Tell the user to change `- [ ]` to `- [x]` on the rows to fix, save the file, and run `/proceso-field-fix`. A Match row has an empty Fix cell. Do not edit the form in this pass.
