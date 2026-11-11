---
name: proceso-field-fix
description: >
  Fixes only the rows whose Fix cell has an x inside the brackets, such as
  `- [x]` or `- [ x]`, in model-result/<processNumber>/filed-validation/report.md. Use when the user
  invokes /proceso-field-fix. Do not use this skill to validate a process or
  to build the legacy knowledge base. Do not fix a row that is still `- [ ]`.
disable-model-invocation: true
---

# Fix the marked rows

This skill runs only when the open folder contains the trees below. The folder name does not matter. Check that before asking questions or editing. If any path is missing, stop. Name each missing path. Tell the user to put these next to each other in the open folder. Do not search outside that folder.

```
<open folder>/
  .cursor/
  legacy/vuc_mtto_aga2/
  legacy/vuc_mtto_commons/
  Project/frontend/
```

This command edits the migrated form. It does not write a new validation report. It does not re-grade fields.

## Ask first

Ask for the process number only.

1. If the user has not given a **process number**, ask for it and stop.
2. Read `model-result/<processNumber>/filed-validation/report.md`. If that file is missing, stop. Tell the user to run `/proceso-field-validation` for that process first.

## Which rows

Take every row whose **Fix** cell has an `x` inside the brackets. These all count: `- [x]`, `- [X]`, `- [ x]`, `- [ x ]`, `[x]`, `[ x]`. The mark has to be saved in that file. A click in a preview does not count. An `x` written outside the brackets does not count.

- Do not fix a row whose Fix cell is `- [ ]` or `[ ]`.
- Do not fix a row with an empty Fix cell.
- A marked UI behavior row and a marked finding with the same name are one fix.
- If no Fix cell has an `x` inside the brackets, stop. Tell the user to put `x` inside the brackets, save, and run `/proceso-field-fix` again.

## Where a fix may be written

Write only inside the migrated process folder confirmed for this process. That folder is the **Migrated tree** in the report. `Project/frontend/apps/agace/src/app/application/tramites/<processNumber>/` is one example. The same limit applies to every process in every department: edit that process folder only, wherever it sits.

Do not edit a common or shared component. That includes `formas-dinamicas`, `input-fecha`, catalog selects, files under `libs/shared`, and any file outside this process folder.

Leave legacy Java, JSP, and JS unchanged. Do not edit a sibling trámite.

If the marked row cannot be fixed inside that process folder, stop before editing. Ask permission with one structured choice, the way a plan asks for approval. Do not ask only in a paragraph. The question names the marked row, the file outside the folder, and the change. The choices are **Allow** and **Do not allow**. Do not edit that file until the user chooses **Allow**.

Prefer a change inside the process folder when it enforces the same legacy rule.

## Removal needs permission

Ask before removing anything that is already in the migrated process, even inside the process folder. That includes a validation, a behavior (shown, hidden, enabled, disabled, or readonly), a component, or a text (heading, label, or message).

Ask with one structured choice, the way a plan asks for approval. Do not ask only in a paragraph. The question names the marked row, the file, and the exact validation, behavior, component, or text that would be removed. The choices are **Allow** and **Do not allow**. Do not remove it until the user chooses **Allow**. Adding or tightening a rule so it matches the legacy behavior does not need this extra permission.

## What the fix changes

Change only the behavior the marked row describes. Do not retune an unmarked field in the same file.

Read `.cursor/knowledge/procesos/<processNumber>/legacy.md` for the legacy rule of each marked row. Read `.cursor/knowledge/legacy-base/architecture.md` for the platform contract (`class="required"` versus a label asterisk, stripes, `parentSelectName`, jquery `ignore: []`). Do not invent a rule that is not in those files or in the marked row.

After the edits, say which marked rows were updated, which files inside the process folder changed, and which marked rows were not changed because they need a file outside that folder or a removal the user has not allowed. Do not rewrite the report. The next `/proceso-field-validation` pass moves a fixed row to **Corrected**.
