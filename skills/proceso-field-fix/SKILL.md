---
name: proceso-field-fix
description: >
  Frontend command. Ask process number, then registration or workflow.
  Fixes only the rows whose Fix cell has an x inside the brackets, such as
  `- [x]` or `- [ x]`, in the report for that surface:
  filed-validation/report.md for registration,
  workflow-validation/report.md for workflow. A workflow pass also reads
  .cursor/knowledge/frontend-base before editing. A registration pass does
  not. Use when the user invokes /proceso-field-fix. Do not use this skill
  to validate a process, to build the legacy knowledge base, or to fix a
  backend GitHub issue. Do not fix a row that is still `- [ ]`. The backend
  fix is /github-ticket-fix-backend.
disable-model-invocation: true
---

# Fix the marked rows

This skill is a frontend command. It edits the new form. It does not fix a backend GitHub issue. That command is `/github-ticket-fix-backend`.

The parent folder must contain `.cursor/` and the old code in `old/`, `legacy/`, or `old code/`. Letter case does not matter. If none of those folders is present, stop. This command does not edit that old code.

The process folder to edit is the **Migrated tree** in the report. When that path is not on this machine, search the parent for `tramites/<processNumber>`, including inside a folder named `new code/`. One match: use it. More than one: list the paths and ask. Stop. None: ask for the path and stop. The new backend may be absent. Do not stop because it is missing.

This command edits the migrated form. It does not write a new validation report. It does not re-grade fields.

## Ask first

Ask for the process number, then whether this pass is **registration** or **workflow**. Ask one question at a time. Do not skip the surface question.

1. If the user has not given a **process number**, ask for it and stop. Do not ask the next question in that same turn.
2. If the user has not said **registration** or **workflow**, ask which this pass is and stop. Registration is the registro wizard (Capturar solicitud, Anexar requisitos, Firmar solicitud). Workflow is the funcionario task flow (evaluar, verificar dictamen, autorizar dictamen, confirmar notificación, autorizar requerimiento, atender requerimiento). To fix both, run this command twice, once per surface, each with its own report.
3. As soon as both are known, read the report and the knowledge base for that surface only. Registration uses `model-result/<processNumber>/filed-validation/report.md` and `.cursor/knowledge/procesos/<processNumber>/legacy.md`. Workflow uses `model-result/<processNumber>/workflow-validation/report.md` and `.cursor/knowledge/procesos/<processNumber>/legacy-workflow.md`. If the report is missing, stop. Tell the user to run `/proceso-field-validation` for that process and that surface first. Do not open the other surface’s report or knowledge base.
4. **Workflow only.** Before editing a marked row, read `.cursor/knowledge/frontend-base`. Open `.cursor/knowledge/frontend-base/workflow.md`. If that file is missing, stop. If the process number is not in **Procedures in scope**, stop. Use it for the new-page contract: department, task URL, shared shell versus procedure body, and **Department:** versus **Procedure:** lines. Do not copy a numbered sentence onto another procedure. Do not guess a path that `workflow.md` does not name. **Registration** skips this step. Do not open `.cursor/knowledge/frontend-base` on a registration pass.

If the invoking message already contains the process number and the surface, do not ask again.

## Which rows

Take every row whose **Fix** cell has an `x` inside the brackets. These all count: `- [x]`, `- [X]`, `- [ x]`, `- [ x ]`, `[x]`, `[ x]`. The mark has to be saved in that file. A click in a preview does not count. An `x` written outside the brackets does not count.

- Do not fix a row whose Fix cell is `- [ ]` or `[ ]`.
- Do not fix a row with an empty Fix cell.
- A marked UI behavior row and a marked finding with the same name are one fix.
- If no Fix cell has an `x` inside the brackets, stop. Tell the user to put `x` inside the brackets, save, and run `/proceso-field-fix` again.

## Where a fix may be written

Write only inside the migrated process folder confirmed for this process. That folder is the **Migrated tree** in the report. `Project/frontend/apps/agace/src/app/application/tramites/<processNumber>/` is one example. The same limit applies to every process in every department: edit that process folder only, wherever it sits.

A file inside `tramites/<processNumber>/` may be edited directly. These six folders may also be edited directly. The parent evaluar page around them is not one of them and still needs Allow:

- `Project/frontend/apps/agricultura/src/app/application/evaluar/components_220102`
- `Project/frontend/apps/agricultura/src/app/application/evaluar/components_220103`
- `Project/frontend/apps/agricultura/src/app/application/evaluar/components_221601`
- `Project/frontend/apps/agace/src/app/application/evaluar/dictamen317`
- `Project/frontend/apps/agace/src/app/application/evaluar/dictamen-31803`
- `Project/frontend/apps/aga/src/app/application/evaluar/dictamen-303`

Do not edit a common or shared component until the user chooses **Allow**. That includes `formas-dinamicas`, `input-fecha`, catalog selects, and files under `Project/frontend/libs/shared` (the inbox, ConsultaioStore, app-reviewers-tabs, solicitante, app-generar-dictamen, firma-electronica, and the wizard buttons). The same Allow applies to a department task shell outside `tramites/<processNumber>/`: evaluar, verificar-dictamen, autorizar-dictamen, autorizar-requerimiento, confirmar-notificacion, proceso-requerimiento, atender-requerimiento, and `tramiteConfig.service.ts`.

On a workflow pass, the funcionario task list and page behavior come from `.cursor/knowledge/frontend-base/workflow.md`. Prefer a change that matches that map for shared shell versus procedure body. The registro wizard (Capturar solicitud, Anexar requisitos, Firmar solicitud) is a different flow and is not fixed on a workflow pass.

Leave legacy Java, JSP, and JS unchanged. Do not edit a sibling trámite.

If the marked row cannot be fixed inside that process folder or one of the six folders, stop before editing. Ask permission with one structured choice, the way a plan asks for approval. Do not ask only in a paragraph. The question names the marked row, the file outside the folder, and the change. The choices are **Allow** and **Do not allow**. Do not edit that file until the user chooses **Allow**.

Prefer a change inside the process folder when it enforces the same legacy rule.

## Removal needs permission

Ask before removing anything that is already in the migrated process, even inside the process folder. That includes a validation, a behavior (shown, hidden, enabled, disabled, or readonly), a component, or a text (heading, label, or message).

Ask with one structured choice, the way a plan asks for approval. Do not ask only in a paragraph. The question names the marked row, the file, and the exact validation, behavior, component, or text that would be removed. The choices are **Allow** and **Do not allow**. Do not remove it until the user chooses **Allow**. Adding or tightening a rule so it matches the legacy behavior does not need this extra permission.

## What the fix changes

Change only the behavior the marked row describes. Do not retune an unmarked field in the same file.

Read in this order before editing:

1. `.cursor/knowledge/legacy-base/architecture.md` for the platform contract (`class="required"` versus a label asterisk, stripes, `parentSelectName`, jquery `ignore: []`).
2. The inventory for this surface: `legacy.md` for registration, `legacy-workflow.md` for workflow.
3. On a **workflow** pass only: `.cursor/knowledge/frontend-base/workflow.md` (and any other file in `.cursor/knowledge/frontend-base` that applies). Use it to place the fix on the shared shell or the procedure body. Do not open that folder on a registration pass.

Do not invent a rule that is not in those files or in the marked row.

## Steps

```
- [ ] Before asking, stop unless the parent contains .cursor and the old code in old/, legacy/, or old code/
- [ ] Ask for the process number when the message does not include one
- [ ] Ask whether this pass is registration or workflow when the message does not say
- [ ] Read the report for that surface; stop if missing
- [ ] Read the inventory for that surface
- [ ] On a workflow pass only, read .cursor/knowledge/frontend-base/workflow.md and stop if it is missing or the process number is not in Procedures in scope. Do not open that folder on a registration pass
- [ ] Take only Fix rows with x inside the brackets
- [ ] Edit only allowed paths; ask Allow for shared or department shell files
- [ ] Ask Allow before removing existing migrated behavior
- [ ] Report which marked rows were updated and which were not; do not rewrite the validation report
```

After the edits, say which marked rows were updated, which files inside the process folder changed, and which marked rows were not changed because they need a file outside that folder or a removal the user has not allowed. Do not rewrite the report. The next `/proceso-field-validation` pass moves a fixed row to **Corrected**.
