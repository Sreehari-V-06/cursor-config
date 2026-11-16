# Process <number> — funcionario workflow

Differences from `.cursor/knowledge/frontend-base/workflow.md` for this procedure only. Do not rebuild the inbox here. Do not copy another procedure’s numbered body onto this number.

## Identity

- Process number: `<number>`
- Department: `<department from tramiteList.json>`
- Evidence: `Project/frontend/libs/shared/theme/assets/json/tramiteList.json`
- In the shared procedure list: `<yes or no>`

## Page

One block per task whose page body differs, or whose page this pass opened because the shared file has no body for this number. Task names: Evaluar, Verificar dictamen, Autorizar dictamen, Confirmar notificación, Autorizar requerimiento, Atender requerimiento.

### <task name>

- Route: `< /${department}/<task> or Not found>`
- What this procedure’s page does: `<behavior>`
- Evidence: `<path>`

## Actions

Buttons, posts, and messages that differ for this procedure. Cite the file.

- `<control or button>` — `<what the code does>` — `<path>`

## Return

Where each success and cancel action goes for this procedure.

- `<action>` — `<inbox, same page, or other route>` — `<path>`

## Not found

Bullets for tasks or cells left as `Not found`, each with the file that was searched. If this pass confirmed every section, write `None`.
