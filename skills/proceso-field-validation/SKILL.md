---
name: proceso-field-validation
description: >
  Frontend command. Ask process number, then registration or workflow.
  Each surface needs its own knowledge base: legacy.md for registration,
  legacy-workflow.md for workflow. One file does not cover the other.
  Order: common legacy architecture, then that process file, then a
  cross-check against the migrated frontend, then a report. A workflow
  pass also reads .cursor/knowledge/frontend-base before the cross-check.
  A registration pass does not.   The cross-check starts at the route and opens every component and every
  modal before the report is written. On both registration and workflow,
  score validation, logic, and UI behavior from the old JSP, the old
  JavaScript (including validate() and regex), and the old .properties
  files (exact Spanish messages). The report records the JS rule and the
  Spanish message when they exist. After the report, the user marks Fix on
  the rows to change. Use when the user invokes /proceso-field-validation
  or asks to validate the form again after fixes. Do not use this skill to
  build the process knowledge base, to edit the form, or to fix a backend
  GitHub issue. The form fix is /proceso-field-fix. The backend fix is
  /github-ticket-fix-backend.
disable-model-invocation: true
---

# Process field validation

This skill is a frontend command. It compares the old page with the new form. It does not fix a backend GitHub issue. That command is `/github-ticket-fix-backend`.

The parent folder must contain `.cursor/` and the old code in `old/`, `legacy/`, or `old code/`. Letter case does not matter. Check that before asking questions or reading code. If none of those folders is present, stop and name the missing path. Do not search outside the parent folder. This command does not read that old tree. Registration and workflow each have a separate knowledge base. Registration uses `.cursor/knowledge/procesos/<processNumber>/legacy.md`. Workflow uses `.cursor/knowledge/procesos/<processNumber>/legacy-workflow.md`. A registration file does not stand in for workflow. A workflow file does not stand in for registration. Build the missing file with `/legacy-proceso-knowledge-base` for that surface.

A registration pass also needs the new form folder for this process. When the user does not give a path, search the parent for `tramites/<processNumber>`, including `Project/frontend/**/tramites/<processNumber>` and a folder named `new code/`. `Project/frontend/apps/agace/src/app/application/tramites/32515/` is one example. Another department uses its own app folder under `Project/frontend`. One match: use it. More than one: list the paths and ask which tree to use. Stop. None: name what is missing, ask for the path, and stop.

On a **workflow** pass only, read `.cursor/knowledge/frontend-base` before opening Angular. That folder is the new-form map for funcionario tasks. Use `.cursor/knowledge/frontend-base/workflow.md` for the task URL, the department shell, and the procedure body those pages mount. A **registration** pass does not open that folder. The new backend may be absent. Do not stop because it is missing.

When an old file has to be opened, open the path in the **Code map** of the inventory for this surface. Do not search `old/`, `legacy/`, or `old code/` for a similar class. A path under **Do not open** is the wrong implementation.

Run these steps in order. Do not cross-check until the knowledge steps for this surface are done. Do not write the report until every page, every component, and every modal on the route has been opened and scored. This pass does not edit code. A form fix is a separate command, `/proceso-field-fix`.

1. **Common knowledge.** Read `.cursor/knowledge/legacy-base/architecture.md`. If that file is missing, stop and say so. Use it for the platform contract (`class="required"` versus a label asterisk, stripes, `parentSelectName`, jquery `ignore: []`). Do not treat a process-script comment as that contract.
2. **Process knowledge.** Read the inventory for the surface the user chose. Registration uses `.cursor/knowledge/procesos/<processNumber>/legacy.md`. Workflow uses `.cursor/knowledge/procesos/<processNumber>/legacy-workflow.md`. This skill runs only when that file is already present. If it is missing, stop even when the other surface’s file exists. Tell the user this skill is not allowed for that surface until its own knowledge base exists, and to build it with `/legacy-proceso-knowledge-base` (process number, that surface, and the old-code controller). Do not build the knowledge base in this skill. Do not invent the legacy inventory. Do not score workflow from `legacy.md`. Do not score registration from `legacy-workflow.md`.
3. **Frontend knowledge (workflow only).** On a workflow pass, read `.cursor/knowledge/frontend-base` before scoring a field. Open `.cursor/knowledge/frontend-base/workflow.md`. If that file is missing, stop. If the process number is not in **Procedures in scope**, stop. Use it for the new-page contract: department, task URL, shared shell versus procedure body, and **Department:** versus **Procedure:** lines. Do not copy a numbered sentence onto another procedure. Then open the Angular files that file names. Do not guess a new-form path that `workflow.md` does not name. On a registration pass, skip this step. Do not open `.cursor/knowledge/frontend-base`.
4. **Cross-check.** Start from the route for that surface. Open every component the screens declare and every modal those components open. Read each of those files to the last line. On the old side, for every page and every modal, open the **JSP**, the **JavaScript** it includes (`validate()`, regex, show/hide, enable/disable, readonly, submit guards), and the **`.properties`** files that hold the Spanish messages those pages use. Same sources for registration and for workflow. Compare field by field and behavior by behavior. A match on the parent screen does not end the check. A one-line summary in the inventory is not the rule for a modal.
5. **Report.** Write the report for that surface only after that list is finished. Registration writes `model-result/<processNumber>/filed-validation/report.md`. Workflow writes `model-result/<processNumber>/workflow-validation/report.md`. Fill **JS rule** and **Spanish message** on every scored validation row when the evidence exists. If one modal was not opened, do not write the report.

## Ask first

Ask for the process number, then whether this pass is **registration** or **workflow**. Ask one question at a time. Do not skip the surface question. Do not ask for the old-code controller.

1. If the user has not given a **process number**, ask for it and stop. Do not ask the next question in that same turn.
2. If the user has not said **registration** or **workflow**, ask which this pass is and stop. Registration is the registro wizard (Capturar solicitud, Anexar requisitos, Firmar solicitud). Workflow is the funcionario task flow (evaluar, verificar dictamen, autorizar dictamen, confirmar notificación, autorizar requerimiento, atender requerimiento). To validate both, run this command twice, once per surface, each with its own knowledge base.
3. As soon as both are known, check the inventory for that surface only. Registration requires `.cursor/knowledge/procesos/<processNumber>/legacy.md`. Workflow requires `.cursor/knowledge/procesos/<processNumber>/legacy-workflow.md`. If that file is missing, stop. This skill is not allowed for that surface until its own knowledge base exists. Tell the user to build it with `/legacy-proceso-knowledge-base` for that same surface. Do not offer to build it here. Do not open the other surface’s file.

If the invoking message already contains the process number and the surface, do not ask again.

The user does not have to give the form path. When they do not, use the folder the search found. When they do, use that path after confirming it is this process.

Confirm the migrated tree is this process: the folder name, a route, or a procedure id constant equals the process number. If it does not, stop and report both values.

## Write

Registration writes `model-result/<processNumber>/filed-validation/report.md`. Workflow writes `model-result/<processNumber>/workflow-validation/report.md`. Both sit at the repository root. A pass updates only the report for the surface it was given.

Fill [report-template.md](report-template.md). Delete none of its sections. Do not write this report under `.cursor/knowledge`.

Write for a person who will choose rows to fix, and for the frontend developer who will make the change. Open with what they can still do on the screen. Under each section heading, one short sentence in everyday language. In the reading columns, say **Old page** and **New page**. The **Item** cell is the full label shown on the screen. Do not shorten it. On **Incorrect**, **Incomplete**, **Missing**, and **Match** rows, fill **JS rule** and **Spanish message** when the old side has them (same columns for registration and workflow). **JS rule** is the regex, jquery rule, or validation condition from the JS (or inline script), quoted as written. **Spanish message** is the exact Spanish text from the `.properties` file (or from a hardcoded Spanish string in the JSP/JS when no properties key exists). Write `Not found` in that cell when the code does not define it. Do not invent a message. The last column is **Where to change**: the file inside this process folder, the field or method, and the change in plain words. Name a shared file only when this form cannot do the change alone. Do not fill that column with a `legacy.md` citation. Group **Incorrect** rows under a short heading when several rows are the same kind of problem. Keep every row.

The first column of **Incorrect**, **Missing**, **Incomplete**, and **UI behavior** is **Fix**. An open row uses `- [ ]`. A chosen row has `x` inside the brackets: `- [x]`, `- [X]`, `- [ x]`, or `[x]`. A Match row leaves that cell empty. An `x` written outside the brackets is not a choice. This mark is plain Markdown so the same file works on any system. Do not use an HTML checkbox, a preview widget, or a plugin. Do not add a second list above the table. When this pass rewrites the report, keep a chosen mark on a row that is still open. Do not edit the form in this pass. The user runs `/proceso-field-fix` after saving the marks.

If the report for this surface already exists, this pass updates that file. Do not start a second report for the same surface. Read the previous **Incorrect**, **Missing**, **Incomplete**, and **Corrected** rows first. Re-check every one of those items in the current migrated tree, then rewrite the file with this pass’s results.

## Steps

```
- [ ] Before asking, stop and name the missing path unless the parent contains .cursor and the old code in old/, legacy/, or old code/
- [ ] Ask for the process number when the message does not include one
- [ ] Ask whether this pass is registration or workflow when the message does not say
- [ ] On a registration pass, when no form path was given, search the parent for tramites/<processNumber>, including Project/frontend/**/tramites/<processNumber> and new code/; name what is missing and ask if the match is missing or not unique
- [ ] Stop if the inventory for that surface is missing: legacy.md for registration, legacy-workflow.md for workflow. Do not use the other surface’s file.
- [ ] Read .cursor/knowledge/legacy-base/architecture.md
- [ ] Read the inventory for that surface
- [ ] On a workflow pass only, read .cursor/knowledge/frontend-base/workflow.md and stop if it is missing or the process number is not in Procedures in scope. Do not open that folder on a registration pass
- [ ] Read the report for that surface when it exists
- [ ] Confirm the migrated tree is this process number
- [ ] List every page on the route, every component those pages declare, and every modal those components open
- [ ] Read each of those files to the last line
- [ ] Open the old JSP, the old JavaScript (validate(), regex, UI logic), and the related .properties files for each page and each modal, to the last line
- [ ] Map each field, including every field inside each modal, to a migrated control
- [ ] Cross-check validation, logic, and UI behavior for each of those fields from JSP + JS + properties
- [ ] Resolve each message key to the exact Spanish text in the .properties file when a key exists
- [ ] Classify each finding, including previously reported items
- [ ] Update the report for that surface only after that list is finished: filed-validation/report.md for registration, workflow-validation/report.md for workflow. Fill JS rule and Spanish message columns
- [ ] Tell the user which modals were opened, then tell them to mark Fix with `- [x]`, save, and run /proceso-field-fix, then stop
```

### Open every screen and every modal

A modal is part of this process. Score every field inside it, and every shown, hidden, enabled, disabled, readonly, and wording rule on those fields. The button that opens the modal is not a substitute for the fields inside it.

On a registration pass, the route is the registro form under `tramites/<processNumber>`, and the old page is the capture JSP named in `legacy.md`. Do not open `.cursor/knowledge/frontend-base`.

On a workflow pass, read `.cursor/knowledge/frontend-base/workflow.md` first. If the process number is not in **Procedures in scope**, stop. The route is the funcionario task URL that file names for this procedure’s department. The old page is the task JSP named in `legacy-workflow.md`. Score the new page against that file and the Angular it names: shared shell versus procedure body, **Department:** versus **Procedure:**. Do not score the registro wizard on a workflow pass.

Build the list before scoring any row:

1. Start at the route for this surface. Name every page and wizard step that route mounts.
2. For each page, open the template and name every component it declares, including each tab.
3. For each component, open the template and the class. Name every modal, dialog, or panel it opens. That includes Agregar, Modificar, a `MatDialog` open, a dialog service, and a child component that is the dialog. Open that dialog’s template and class.
4. Repeat when a dialog opens another dialog.
5. Read each of those files to the last line. If a read stops early, read the rest before any row is scored. A control past the cutoff is not Missing and is not skipped.
6. On the old side, open the page named in the inventory, every JSP it includes, and every JSP a script loads into a dialog. Open every **JavaScript** file those pages include under this process (and the inline script in the JSP). Open the `validate()` block, or the script function that dialog calls, to the last line. Read every **regex**, jquery `rules` / `messages`, maxlength, show/hide, enable/disable, readonly, and submit guard in that JS. Do not score a dialog from a one-line summary in the inventory. When that summary and the JSP or `validate()` disagree, the JSP and `validate()` are the rule.
7. Open the **`.properties`** files that supply the Spanish text for those pages. Prefer paths in the inventory **Code map**. Also open a properties file that an opened JSP or JS names (`fmt:message`, `fmt:bundle`, Stripes label bundle, `getText`, a jquery message key that maps to a bundle). Prefer the Spanish locale file when both exist (`*_es*.properties`, or the default `StripesResources.properties` / module resources that hold Spanish). Quote the **exact Spanish value** for the key. If the key is absent, write `Not found`. Do not invent Spanish wording. Hardcoded Spanish in the JSP or JS counts as the message and the properties cell is `Not found` (or note “hardcoded in JSP/JS”).
8. Related dialogs stay separate. An agent dialog, an apoderado dialog, and an agency dialog are three forms. Score each field on the form it belongs to.
9. A parent match does not close the list. A Continuar rule that stops when a list is empty does not replace the dialog behind Agregar. Finish the dialog after the parent row is scored.
10. In the report, **Tab** is the tab that opens the dialog. **Section** is the dialog title. One row per field and per behavior, the same as a field on the main screen. Put the JS regex or condition in **JS rule**. Put the resolved Spanish text in **Spanish message**.

Write the report only after every component and every modal on that list has been scored.

### Map fields

Match a legacy **Fields** row to a migrated control by label, then by the posted name or model path. Include every field inside every modal from the list above. Search the trámite folder, including dialog components, for `validadores`, `Validators`, `inputFilter`, `required`, and the method that blocks Continuar, Guardar, or the dialog’s accept action.

A legacy field with no migrated control is **Missing**. A migrated control with no legacy row is listed under **Only in the migrated form**. Do not call that a legacy gap.

Skip **Dead selectors** as migrated gaps when the legacy file says the id is not in the capture page. List them under **Correctly absent**.

### Compare each field

Compare the legacy columns to the migrated control. Sources on the old side are always **JSP + JavaScript + `.properties`**, for registration and for workflow. On a workflow pass, also use `.cursor/knowledge/frontend-base/workflow.md` for the new-page contract (where the field lives, shared shell versus procedure body, department versus procedure). On a registration pass, compare against the trámite folder only for the new side. Quote both patterns when they differ. Give one concrete value that one side accepts and the other rejects before calling the row **Incorrect**.

Run the same checks on every field inside every modal. Record a finding only where they differ:

- Required when, including a role or visibility condition (JSP `class="required"`, stripes `@Validate`, or JS that adds `required`)
- Shown when and hidden when. If a branch shows or hides a container, list every input inside that container. A field that is on screen only for one role is not the same as a field that is always rendered, even when both are required for the shown role. Read show/hide from JSP `c:if` / style and from JS
- Enabled when and disabled when. A field that stays on screen but cannot be used is not a hidden field
- Readonly when. A readonly field that a calendar or another control still fills is not a disabled field, and it is not a field the user can type
- Headings and other text. If a heading, label, or message is inside a shown or hidden container, list that text with the fields. If the wording changes with a role, tab, or other condition, record both wordings. A heading that stays on screen with the same words is not a hidden heading. Resolve `fmt:message` / label keys through `.properties`
- Put each shown, hidden, enabled, disabled, or readonly field, and each heading or text, in the UI behavior section. One row per item and per behavior. Set **Kind** to `Text` for a heading, label, or message, and `Field` for an input or other control. `Always` when that state does not change. Do not treat “required when a role is selected” as a substitute for shown, hidden, enabled, disabled, readonly, or a heading. If that same mismatch is already in Incorrect, Missing, or Incomplete, do not count it a second time
- Max length (JSP `maxlength`, stripes maxlength, or JS truncate)
- Character filter (keypress, keyup, or `inputFilter`)
- Paste. That filter does not see a paste. On the new page, `inputFilter` runs on keypress only, and Ctrl+V is let through. On the old page, `onkeypress` misses a paste the same way. For every character the filter blocks, record whether a paste stays in the field and whether Continuar or Guardar saves it. A pattern, a blur clear, or an input listener may still stop it. If both pages save that paste, say so on the row and do not open a second finding. If the new page saves a paste the old blur check cleared, the row is **Incorrect**. If the character stays visible and the save is still blocked, say that on the row
- Validator regex and validation conditions from **JavaScript** (jquery `rules`, custom methods, `/regex/`, blur handlers). Put the exact pattern or condition in the report **JS rule** cell
- Message: resolve the key to the **exact Spanish** string in the `.properties` file and put it in **Spanish message**. Compare that wording to the new form message. A different Spanish sentence with the same blocking outcome is still a message mismatch when the row is about wording; use **Incomplete** when the block matches and only the text differs, unless the inventory treated the text as the rule
- On failure: clear the field, mark the control invalid, or alert only
- Server check (lookup, date parse) and whether failure blocks save
- Catalog parent: `parentSelectName` versus lists loaded independently

Also compare **Role branches**, **Submit validation**, and **Documents** from the inventory for this surface. A submit path that never runs the migrated validators is **Incorrect** even when each control has a validator.

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

Every finding names the file inside this process folder, the field or method, and the change. Do not grade a sibling trámite. Do not treat a label asterisk alone as a legacy required rule; the inventory for this surface already applied that distinction.

## After writing

Tell the user the report path for this surface and the counts: match, corrected, incorrect, incomplete, missing. Lead with **Corrected** when this pass closed a previous finding, then **Incorrect**, **Missing**, and **Incomplete**.

Name every modal that was opened and scored. If this process has no modal, say so.

Then stop. Tell the user to change `- [ ]` to `- [x]` on the rows to fix, save the file, and run `/proceso-field-fix`. A Match row has an empty Fix cell. Do not edit the form in this pass.
