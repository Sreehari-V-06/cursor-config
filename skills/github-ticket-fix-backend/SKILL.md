---
name: github-ticket-fix-backend
description: >
  Fixes one GitHub issue in the new backend module for that process. Use when
  the user invokes /github-ticket-fix-backend or gives a GitHub issue URL or
  number and asks to fix that backend ticket. Read the issue, take the process
  id, read the platform and process knowledge, compare the old Java process
  with the new module on this machine, and edit only the backend behavior the
  ticket describes. Folder names differ by machine. Do not use this skill for
  a frontend or form fix, to build the legacy knowledge base, to validate form
  fields, or to open a pull request. Form validation is
  /proceso-field-validation. A form fix is /proceso-field-fix.
disable-model-invocation: true
---

# Fix one GitHub issue in the backend

This skill is a backend fix. It edits Java in the new backend module for this process. It does not edit the form, JSP, or JS. If the ticket is a form or page mismatch, stop. Tell the user to run `/proceso-field-fix`. Do not look for the form on this machine.

The parent folder must contain `.cursor/` and the old code in `old/` or `legacy/`. Letter case does not matter. If neither old-code folder is present, stop. Say the parent must contain `.cursor/` and the old code in `old/` or `legacy/`. Do not look for the old code anywhere else.

The form may be absent. This command also needs the new backend module for this process. A folder named `ms-sat-t<process id>` is one example. That module's parent path differs by machine. Search the parent for it. One match: use it. More than one: list the paths and ask. Stop. None: ask for the path and stop.

This command edits the new backend process module. It does not open a pull request. It does not commit. It may add one confirmed row to the process code map. It does not rewrite the rest of that file, an analysis card, or the ticket.

Ask one question at a time. Stop after the question.

## Ask first

1. If the user has not given a **GitHub issue** (URL or number), ask for it and stop. Do not scan the repository for a ticket.
2. Read the issue with `gh issue view`. Use the title and body only. If that command fails, stop and say so.

## Process id

Take the trámite number from the title and body. Examples: `220701`, `32515`.

- If the ticket names more than one process number, list them and ask which one. Stop.
- If the ticket names none, ask for the process number and stop.

## Knowledge

Read these in order. Do not compare code until this step is done.

1. **Platform.** Read `.cursor/knowledge/legacy-base/architecture.md`. If that file is missing, stop and say so. Use it for the platform contract. Do not treat a process-script comment as that contract.
2. **Process.** Read `.cursor/knowledge/procesos/<process id>/legacy.md`. Do not build the file in this skill. If it is missing, stop. Tell the user to run `/legacy-proceso-knowledge-base` for this process first. The old tree is too large to pick the implementation by a fresh search.
3. **Prior cards.** Read every file already at `knowledge/analysis/<process id>/*.md`. A card is a prior note. Re-check the code before treating a sentence in it as the cause.

## Old code

Open the **Code map** row for the behavior the ticket names. Read that file and method. Do not search `old/` or `legacy/` for another class with a similar name. Do not open a path listed under **Do not open**.

If the map has no row for that behavior, follow calls from the controller named in the same `legacy.md`. Do not search the rest of the old tree. When that follow proves the file and method, add one row to the code map: behavior, class, method, file, and any sibling you rejected under **Do not open**. Leave the other rows unchanged.

Leave JSP and JS unread unless that Java calls them for this behavior. Do not edit the old code.

Read a commons parent only when the code map names it, or when `.cursor/knowledge/legacy-base/architecture.md` says that parent applies and this process does not override the method. Do not open a sibling trámite to fill a gap.

## New code

Search the open folder for the new backend module for this process. A folder named `ms-sat-t<process id>` is one example. Read the controller, service, rules, repository, and request for that same behavior. One match: use it. More than one: list the paths and ask. Stop. None: ask for the path and stop.

## Cause

State the ticket behavior, what the old process does, and what the new process does.

Fix only when those two differ on the behavior the ticket describes. If they already match, stop and say so. Do not invent a rule that is not in the ticket, the knowledge files, or those two code paths.

## Where a fix may be written

Write only backend code inside the new process module confirmed above.

Leave the old code unchanged. Do not edit the form, JSP, or JS. Do not edit a sibling process module. Do not edit a shared module, whatever its parent folder is called. That includes `ms-tramite-flujo`, `ms-dictamen`, `common-model`, and the other shared services. The **Allow** choice below is for a shared backend module. It does not cover the form.

If the process module cannot enforce the old behavior, stop before editing. Ask permission with one structured choice, the way a plan asks for approval. Do not ask only in a paragraph. The question names the ticket, the file outside the process module, and the change. The choices are **Allow** and **Do not allow**. Do not edit that file until the user chooses **Allow**.

Change only the behavior the ticket describes. Do not retune an unmarked path in the same file.

## Steps

```
- [ ] Stop unless the parent contains .cursor and the old code in old/ or legacy/
- [ ] Find the new backend module in the parent; ask if that match is missing or not unique
- [ ] Ask for the GitHub issue when the message does not include one
- [ ] Read the issue with gh issue view
- [ ] Take the process id, or ask when it is missing or ambiguous
- [ ] Read .cursor/knowledge/legacy-base/architecture.md
- [ ] Stop if .cursor/knowledge/procesos/<process id>/legacy.md is missing and name /legacy-proceso-knowledge-base
- [ ] Open the code-map file for the ticket behavior; do not search the old tree for a similar class
- [ ] Read knowledge/analysis/<process id>/*.md when those cards exist
- [ ] Stop if the ticket is a form or page mismatch and name /proceso-field-fix
- [ ] Read the old Java for the ticket behavior
- [ ] Read the new backend module for that same behavior
- [ ] Stop if the old and new behavior already match
- [ ] Edit only that backend behavior inside the confirmed process module
- [ ] Say the process id, the cause, the files changed, and the old behavior the new code now follows
```

## After the edit

Say the process id, the cause, the files changed, and the old behavior the new code now follows. If this pass added a code-map row, name that row. Do not rewrite the rest of `legacy.md`, the analysis cards, or the ticket.
