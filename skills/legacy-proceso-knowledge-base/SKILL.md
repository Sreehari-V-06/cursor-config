---
name: legacy-proceso-knowledge-base
description: >
  Builds or corrects the process knowledge base so a later agent can open the
  correct old file without searching the whole old tree. Use when anyone who
  has the old code gives a process number (such as 32515), says whether the
  pass is registration or workflow, and gives a controller class or path, and
  asks for a knowledge base, process map, or legacy field and validation
  inventory. If that surface’s knowledge base already exists, ask whether to
  create it again; do not rebuild until the user says yes. The old code is
  under old/, legacy/, or old code/. Open every dialog the capture page opens
  and write one field row per input inside it, from that dialog’s JSP and
  validate(). Read-only on Java, JSP, JS, and Spring XML. Do not use this
  skill to validate the form or to fix a backend GitHub issue. Form
  validation is /proceso-field-validation. The backend fix is
  /github-ticket-fix-backend.
disable-model-invocation: true
---

# Legacy process knowledge base

This skill writes one knowledge base for one process and one surface. Registration and workflow each need their own file. Registration writes `.cursor/knowledge/procesos/<processNumber>/legacy.md`. Workflow writes `.cursor/knowledge/procesos/<processNumber>/legacy-workflow.md`. `/proceso-field-validation` asks which surface to score and reads only that file. The old tree is large, and a similar class in a sibling trámite is the wrong implementation. The knowledge base exists so a later agent opens the file and method this process actually runs, and does not search `old/`, `legacy/`, or `old code/` for another match.

A backend developer and a frontend developer both use it. It does not validate the form. It does not fix a backend GitHub issue. Those commands are `/proceso-field-validation` and `/github-ticket-fix-backend`. They read this file first.

The parent folder is the folder opened in Cursor. It must contain `.cursor/` and the old code. The old code is `old/`, `legacy/`, or `old code/`. Letter case does not matter. One of those is enough. Check that before asking questions or reading code. If none of them is present, stop and name the missing path. Say the parent must contain `.cursor/` and the old code in `old/`, `legacy/`, or `old code/`. Do not look for the old code anywhere else. Do not search outside that folder.

The new form and the new backend may be absent. When they are present, they may sit in a folder named `new code/`. Do not stop because they are missing. Do not search for them.

Build one knowledge base for the legacy trámite the user names. Read code. Do not edit Java, JSP, JS, or Spring XML.

The user must supply the process number, the surface, and the controller. Ask one question at a time. Do not read process code until the knowledge-base overwrite is allowed (or the file is missing) and the controller is known.

1. If the user has not given a **process number**, ask for it and stop. Do not ask the next question in that same turn.
2. If the user has not said **registration** or **workflow**, ask which this pass is and stop. Registration is the registro wizard (Capturar solicitud, Anexar requisitos, Firmar solicitud). Workflow is the funcionario task flow (evaluar, verificar dictamen, autorizar dictamen, confirmar notificación, autorizar requerimiento, atender requerimiento).
3. As soon as the process number and the surface are known, check whether that surface’s knowledge base already exists. Registration checks `.cursor/knowledge/procesos/<processNumber>/legacy.md`. Workflow checks `.cursor/knowledge/procesos/<processNumber>/legacy-workflow.md`. Check only the file for the surface chosen. The other surface’s file does not count.
   - If that file **exists**, do not rebuild it yet. Tell the user the path. Ask whether they want to **create it again** and stop. Do not ask for the controller in that same turn.
   - If the user says **no** (or keep / leave / skip), stop. Leave the existing file and the index rule unchanged. Do not read process code.
   - If the user says **yes** (or rebuild / recreate / overwrite), continue.
   - If that file is **missing**, continue. Do not ask about creating again.
4. After the file is missing or the user said yes to create again, if the user has not given the **controller** (class name or path), ask for it and stop. For registration, that controller is the registro controller. For workflow, that controller is the task controller this pass documents.

If the invoking message already contains the process number, the surface, and the controller, still run step 3 before reading code. If the knowledge base exists and the user has not answered create-again, ask that and stop even when the controller was given. Do not scan every trámite to guess.

A registration pass writes `legacy.md` and leaves `legacy-workflow.md` unchanged. A workflow pass writes `legacy-workflow.md` and leaves `legacy.md` unchanged. To validate both surfaces later, run this command twice: once for registration, once for workflow.

## Write these two files

1. Knowledge base: `.cursor/knowledge/procesos/<processNumber>/legacy.md` for registration, or `.cursor/knowledge/procesos/<processNumber>/legacy-workflow.md` for workflow.
   Fill [template.md](template.md). Replace every placeholder. Delete none of its sections. Set **Surface** to `registration` or `workflow`. On a workflow pass, the registro URL, the registro controller, and the registro wizard are `Not found` unless this task controller is that wizard.
2. Index rule: the existing `.cursor/rules/proceso-<processNumber>*.mdc`, or `.cursor/rules/proceso-<processNumber>.mdc` when none exists.
   `alwaysApply: false`. Globs cover the process package, its JSP and JS trees, and the knowledge base path. The rule body is only the identity block from the template plus two instructions: read the knowledge base before changing the trámite, and open only the files named in its code map. One rule per process number. Do not add a second file beside an existing `proceso-<processNumber>*.mdc`.

Only rewrite the knowledge base when it was missing or the user said yes to create it again. Do not refresh an existing file on your own. When rewriting, fill from the code. Outside the code map, keep a sentence only when this pass still finds the evidence. Do not leave a claim from an older pass that this pass did not confirm. Inside the code map, a **File** cell that points at a sibling or at a parent this process does not run is wrong: replace it with the implementation this pass proved, and put the rejected path in **Do not open**. Add a row for a behavior this pass confirmed and the map did not list. Keep a code-map row this pass did not re-check. Do not drop a confirmed path because a wider search found a similar class.

## Steps

Copy this checklist and mark each item as you finish it:

```
- [ ] Before asking, stop and name the missing path unless the parent contains .cursor and the old code in old/, legacy/, or old code/
- [ ] Do not stop because the new form or the new backend is missing
- [ ] Stop unless the open folder contains .cursor, legacy/vuc_mtto_commons, and Project/frontend
- [ ] Ask for the process number when the message does not include one
- [ ] Ask whether this pass is registration or workflow when the message does not say
- [ ] When that surface’s knowledge base exists, ask whether to create it again and stop; if the user says no, leave the file and stop
- [ ] Ask for the controller when the message does not include one (only after the file is missing or the user said yes)
- [ ] Read .cursor/knowledge/legacy-base/architecture.md
- [ ] Resolve the controller file
- [ ] Prove the process number
- [ ] Bind service, rules, repository, helper, entity
- [ ] Confirm the module pom artifact against the architecture file
- [ ] Read the capture JSP and the JS it includes
- [ ] Open every dialog those pages open, and the JSP and validate() for each dialog, to the last line
- [ ] Extract every live field and every dead selector, including every field inside each dialog
- [ ] Extract role branches, submit scope, documents, persistence, reports
- [ ] Write the code map: one correct file and method per behavior, siblings under Do not open
- [ ] When rewriting an existing file the user approved, drop a non-map claim this pass did not confirm, correct a wrong File cell, add confirmed rows it lacks, and keep a code-map row this pass did not re-check
- [ ] Write legacy.md for registration, or legacy-workflow.md for workflow, from the template
- [ ] Write the index rule
```

### 0. Read the common base first

Read `.cursor/knowledge/legacy-base/architecture.md` before opening process code.

If that file is missing, stop and say so. Do not rebuild the platform chapter inside the process file.

On a registration pass, use the registro parent chain in that file only when the named controller extends `RegistroSolicitudController`. If it extends a different class, record that parent and do not force the registro chain. On a workflow pass, the named controller is a task controller. Record the parent it extends. Do not force the registro chain. Do not open the registro wizard to fill this file.

### 1. Resolve the controller

A path is the file to open. A class name is a search for `<ClassName>.java` inside `old/`, `legacy/`, and `old code/` only.

One file: use it. More than one: list the paths and ask which controller to use. Stop. None: ask for the path and stop. Do not search the rest of the parent.

### 2. Prove the process number

Find the discriminator on the entity (`@DiscriminatorValue`), a process constant, or the Spring `solicitud.discriminatorValue`. The controller the user named must belong to that entity’s package.

If the number and the controller disagree, report both evidence paths and stop. Do not merge two trámites into one knowledge base.

### 3. Bind this process only

From the registro controller, record the service returned by `getRegistroSolicitudServices()` (or the equivalent getter on this class). From that service, record `getRegistroSolicitudRules()` and `getRegistroSolicitudRepository()`.

Follow a method only when this class calls it. An import that is never referenced is unused. Say so, and do not document that type as a dependency.

Sibling process numbers in the same parent folder are other trámites. Name them under **Do not use**. Do not open their services or rules to fill this knowledge base. A class with a similar name in a sibling package is the wrong implementation. Record that path in the code map under **Do not open**.

### 4. Commons parent

Read the module `pom.xml` and record the base `artifactId`. That artifact must match one tree named in `.cursor/knowledge/legacy-base/architecture.md`. That file may say `legacy/vuc_mtto_commons/base-jms-ibm`. On this machine the same module is under `old/`, `legacy/`, or `old code/`. Open the copy in the old-code folder already chosen. One match: open it. More than one: list the paths and ask. Stop. None: ask for the path and stop. Open only that tree. Do not describe a method from another tree.

For each method the architecture file lists on the controller, service, and rules parents, write one line: `not overridden`, or `overridden — <effect>`. Cite the process class that overrides it. Do not paste parent source. Do not copy the variant list, the stripes contract, or the `parentSelectName` contract into the process file. A tab switch before Guardar is process behavior: record it under **Submit validation** only when this trámite’s script does it.

### 5. Capture surface

On a registration pass, this is the registro capture page. On a workflow pass, this is the page the named task controller returns. Do not open the registro wizard on a workflow pass.

From the controller and its JSP constants, open that page and every JSP it includes for this process. Open each script tag under this process’s JS folder. Ignore a script that no JSP for this process includes.

Open every dialog the capture page or those scripts open. That includes a JSP a script loads into a dialog, a hidden dialog div, and the `validate()` function that dialog calls. Read each of those files to the last line. A dialog is part of this process. Do not stop at the main wizard. Related dialogs stay separate: an agent dialog, an apoderado dialog, and an agency dialog are three forms.

### 6. Fields

One row per input, select, or textarea that is rendered, including every input inside a dialog. Read the attributes and the JS that touches that id. Column rules are in the template.

Do not collapse a dialog into one summary line. Two dialogs that share a label are two rows. Name the dialog in Evidence. A summary that disagrees with the dialog JSP or its `validate()` is not the rule. Write the row from those files.

Also list selectors that JS marks required or validates when no element with that id exists in the capture JSP. Those rows go under **Dead selectors**, not under **Fields**.

### 7. Behavior that is not one input

Record, with the file that implements each one:

- A branch that shows, hides, or adds or removes `required` (the condition expression as written)
- What runs on Guardar or the wizard next action, including a tab switch before validation
- AJAX checks (RFC lookup, catalog child load) and the message the user sees
- Document rules that return true or false from this process’s service
- The helper mapping from the request parameter to the persisted property
- Which report bean is chosen, and the condition

## Code map

Fill the **Code map** section of [template.md](template.md). One row per behavior this process runs. Start from the controller the user named and follow only the methods that class calls.

- **File** is the class that implements that behavior for this process number, and the method on it.
- A commons parent is **File** only when this process does not override the method and `.cursor/knowledge/legacy-base/architecture.md` says that parent applies. When this process overrides it, **File** is the override.
- **Do not open** is a sibling or a parent that looks alike and is not this process's implementation. Use `Not found` when there is no such lookalike.
- Do not add a row from a search that matched only by class name.

## Evidence

Every filled cell cites a path. Use `Not found` when the code does not say. Do not infer a maxlength, a regex, or a required flag from a sibling trámite or from a label asterisk alone. A `class="required"` is evidence. An asterisk without that class is a label, and the row says so.

## Example

Input: process `32515`, controller `RegistroSolicitudAvisoValorProvisionalController`.

Writes `.cursor/knowledge/procesos/32515/legacy.md` when that file is missing, or when it exists and the user said yes to create it again. Refreshes the existing `.cursor/rules/proceso-32515-aviso-valor-provisional.mdc` instead of adding a second rule. A workflow pass for the same number writes `legacy-workflow.md` and leaves `legacy.md` unchanged. If `legacy.md` already exists and the user says no, the pass stops and does not rewrite.

A field row that meets the bar:

| UI label | DOM id | Request param | Model path | Required when | Max | Filter | Validator | Message | On failure | Server | Evidence |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Factor aplicable | `factorAplicable` | `factorAplicable` | `solicitud.direccionST.fax` | Always (`class="required"` and `ponerRqueridos`) | 22 | Not found | `/^\d{1,3}(\.\d{1,18})?$/` on blur | `No cumple con el formato esperado 999.999999999999999999` | Clears the input | Stored on the session DTO; no second check | `datosAviso.jsp`; `validaFacAp` in `registrarSolicitud.js` |

## After writing

Tell the user the two paths, the surface, the code-map rows, and every section whose cells are `Not found`. Name every dialog that was opened. If the page has no dialog, say so. Do not start a form comparison, a frontend comparison, or a backend fix in this skill. Later commands open the code-map files. They add one row when they confirm a file the map does not list. They do not search the whole old tree. Field validation against the migrated tree is the `proceso-field-validation` skill. A registration pass requires this `legacy.md`. A workflow pass requires this `legacy-workflow.md`.
