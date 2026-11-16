---
name: frontend-workflow-knowledge-base
description: >
  Read-only on Angular. Builds or corrects one process note for the funcionario
  task flow from Project/frontend. Use when the user invokes
  /frontend-workflow-knowledge-base or asks for the funcionario workflow
  knowledge base for a procedure number. If the number is not in
  .cursor/knowledge/frontend-base/workflow.md, stop. Do not use this skill to
  edit TypeScript, HTML, SCSS, or Java, to document the registro wizard, or to
  fix a form field. The form fix is /proceso-field-fix.
disable-model-invocation: true
---

# Funcionario workflow knowledge base

This skill is read-only on Angular. It does not edit TypeScript, HTML, SCSS, or Java.

Read `.cursor/knowledge/frontend-base/workflow.md` first. The registro wizard (Capturar solicitud, Anexar requisitos, Firmar solicitud) is a different flow. Do not document it here.

The shared file already describes the inbox, the origin map, and the task names. A process file records only where that procedure’s page body differs. Do not rebuild the inbox there. Do not copy a numbered paragraph from one procedure onto another number.

## Ask first

Ask for the process number only.

1. If the user has not given a **process number**, ask for it and stop.
2. Read `.cursor/knowledge/frontend-base/workflow.md`. If the process number is not in the **Procedures in scope** table, stop. Tell the user the number is not in that list. Do not write a page body for it. Do not invent the procedure.

## Write this file

`.cursor/knowledge/procesos/<processNumber>/frontend-workflow.md`

Fill [template.md](template.md). Keep the sections Identity, Page, Actions, Return, and Not found. Replace every placeholder.

Read the Angular code under `Project/frontend` for that procedure only. Cite the file for every behavior. Use `Not found` when the code does not say. Do not invent a route, a button, a message, or a flag.

Same task names as the shared file apply: evaluar, verificar-dictamen, autorizar-dictamen, confirmar-notificacion, autorizar-requerimiento, proceso-requerimiento. The shared file is the common shell for every procedure in the list. Write a section only where this procedure’s page differs from that shell. Leave a section `Not found` when this pass did not open that task for this number.

Procedure-specific branches for 300105, 32401, 420101, and 130118 stay in the shared `onFilaClickTareas` description. Do not copy them into this process file unless the process number is that ID. Those IDs are not in the owned list.

## Where a later fix may be written

This skill does not edit Angular. When a later command edits code, these limits apply.

A file inside `tramites/<procedure>/` may be edited directly.

These six folders may also be edited directly. The parent evaluar page around them is not one of them and still needs Allow:

- `Project/frontend/apps/agricultura/src/app/application/evaluar/components_220102`
- `Project/frontend/apps/agricultura/src/app/application/evaluar/components_220103`
- `Project/frontend/apps/agricultura/src/app/application/evaluar/components_221601`
- `Project/frontend/apps/agace/src/app/application/evaluar/dictamen317`
- `Project/frontend/apps/agace/src/app/application/evaluar/dictamen-31803`
- `Project/frontend/apps/aga/src/app/application/evaluar/dictamen-303`

Ask Allow before editing `Project/frontend/libs/shared` (the inbox, ConsultaioStore, app-reviewers-tabs, solicitante, app-generar-dictamen, firma-electronica, and the wizard buttons) or a department task shell outside `tramites/<procedure>/`: evaluar, verificar-dictamen, autorizar-dictamen, autorizar-requerimiento, confirmar-notificacion, proceso-requerimiento, atender-requerimiento, and tramiteConfig.service.ts.

## After writing

Tell the user the process-file path and every section left as `Not found`. Do not start a form fix in this skill.
