---
name: legacy-proceso-knowledge-base
description: >
  Builds a legacy knowledge base for one VUCEM trámite from its process number
  and controller. Use when the user gives a process number (such as 32515) and
  a controller class or path and asks for a knowledge base, process map, or
  legacy field and validation inventory. Read-only on Java, JSP, JS, and Spring XML.
disable-model-invocation: true
---

# Legacy process knowledge base

This skill runs only when the open folder contains the trees below. The folder name does not matter. Check that before asking questions or reading code. If any path is missing, stop. Name each missing path. Tell the user to put these next to each other in the open folder. Do not search outside that folder.

```
<open folder>/
  .cursor/
  legacy/vuc_mtto_aga2/
  legacy/vuc_mtto_commons/
  Project/frontend/
```

Build one knowledge base for the legacy trámite the user names. Read code. Do not edit Java, JSP, JS, or Spring XML.

The user must supply both. Ask one question at a time. Do not read code until both answers are in the conversation.

1. If the user has not given a **process number**, ask for it and stop. Do not ask for the controller in that same turn.
2. After the process number is known, if the user has not given the **controller** (class name or path), ask for it and stop.

If the invoking message already contains both, do not ask again. Use those values. Do not scan every trámite to guess.

## Write these two files

1. Knowledge base: `.cursor/knowledge/procesos/<processNumber>/legacy.md`
   Fill [template.md](template.md). Replace every placeholder. Delete none of its sections.
2. Index rule: the existing `.cursor/rules/proceso-<processNumber>*.mdc`, or `.cursor/rules/proceso-<processNumber>.mdc` when none exists.
   `alwaysApply: false`. Globs cover the process package, its JSP and JS trees, and the knowledge base path. The rule body is only the identity block from the template plus the instruction to read the knowledge base before changing the trámite. One rule per process number. Do not add a second file beside an existing `proceso-<processNumber>*.mdc`.

If either file already exists, refresh it from the code. Keep a sentence only when this pass still finds the evidence. Do not leave a claim from an older pass that this pass did not confirm.

## Steps

Copy this checklist and mark each item as you finish it:

```
- [ ] Stop unless the open folder contains .cursor, legacy/vuc_mtto_aga2, legacy/vuc_mtto_commons, and Project/frontend
- [ ] Ask for the process number when the message does not include one
- [ ] Ask for the controller when the message does not include one
- [ ] Read .cursor/knowledge/legacy-base/architecture.md
- [ ] Resolve the controller file
- [ ] Prove the process number
- [ ] Bind service, rules, repository, helper, entity
- [ ] Confirm the module pom artifact against the architecture file
- [ ] Read the capture JSP and the JS it includes
- [ ] Extract every live field and every dead selector
- [ ] Extract role branches, submit scope, documents, persistence, reports
- [ ] Write legacy.md from the template
- [ ] Write the index rule
```

### 0. Read the common base first

Read `.cursor/knowledge/legacy-base/architecture.md` before opening process code.

If that file is missing, stop and say so. Do not rebuild the platform chapter inside the process file.

Use the registro parent chain in that file only when the named controller extends `RegistroSolicitudController`. If it extends a different class, record that parent and do not force the registro chain.

### 1. Resolve the controller

A path is the file to open. A class name is a search for `legacy/**/<ClassName>.java`.

Stop if the search returns more than one file. List the paths and ask which controller to use.

### 2. Prove the process number

Find the discriminator on the entity (`@DiscriminatorValue`), a process constant, or the Spring `solicitud.discriminatorValue`. The controller the user named must belong to that entity’s package.

If the number and the controller disagree, report both evidence paths and stop. Do not merge two trámites into one knowledge base.

### 3. Bind this process only

From the registro controller, record the service returned by `getRegistroSolicitudServices()` (or the equivalent getter on this class). From that service, record `getRegistroSolicitudRules()` and `getRegistroSolicitudRepository()`.

Follow a method only when this class calls it. An import that is never referenced is unused. Say so, and do not document that type as a dependency.

Sibling process numbers in the same parent folder are other trámites. Name them under **Do not use**. Do not open their services or rules to fill this knowledge base.

### 4. Commons parent

Read the module `pom.xml` and record the base `artifactId`. That artifact must match one tree named in `.cursor/knowledge/legacy-base/architecture.md`. Open only that tree. Do not describe a method from another tree.

For each method the architecture file lists on the controller, service, and rules parents, write one line: `not overridden`, or `overridden — <effect>`. Cite the process class that overrides it. Do not paste parent source. Do not copy the variant list, the stripes contract, or the `parentSelectName` contract into the process file. A tab switch before Guardar is process behavior: record it under **Submit validation** only when this trámite’s script does it.

### 5. Capture surface

From the controller and its JSP constants, open the capture JSP and every JSP it includes for this process. Open each script tag under this process’s JS folder. Ignore a script that no JSP for this process includes.

### 6. Fields

One row per input, select, or textarea that is rendered. Read the attributes and the JS that touches that id. Column rules are in the template.

Also list selectors that JS marks required or validates when no element with that id exists in the capture JSP. Those rows go under **Dead selectors**, not under **Fields**.

### 7. Behavior that is not one input

Record, with the file that implements each one:

- A branch that shows, hides, or adds or removes `required` (the condition expression as written)
- What runs on Guardar or the wizard next action, including a tab switch before validation
- AJAX checks (RFC lookup, catalog child load) and the message the user sees
- Document rules that return true or false from this process’s service
- The helper mapping from the request parameter to the persisted property
- Which report bean is chosen, and the condition

## Evidence

Every filled cell cites a path. Use `Not found` when the code does not say. Do not infer a maxlength, a regex, or a required flag from a sibling trámite or from a label asterisk alone. A `class="required"` is evidence. An asterisk without that class is a label, and the row says so.

## Example

Input: process `32515`, controller `RegistroSolicitudAvisoValorProvisionalController`.

Writes `.cursor/knowledge/procesos/32515/legacy.md` and refreshes the existing `.cursor/rules/proceso-32515-aviso-valor-provisional.mdc` instead of adding a second rule.

A field row that meets the bar:

| UI label | DOM id | Request param | Model path | Required when | Max | Filter | Validator | Message | On failure | Server | Evidence |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Factor aplicable | `factorAplicable` | `factorAplicable` | `solicitud.direccionST.fax` | Always (`class="required"` and `ponerRqueridos`) | 22 | Not found | `/^\d{1,3}(\.\d{1,18})?$/` on blur | `No cumple con el formato esperado 999.999999999999999999` | Clears the input | Stored on the session DTO; no second check | `datosAviso.jsp`; `validaFacAp` in `registrarSolicitud.js` |

## After writing

Tell the user the two paths and list every section whose cells are `Not found`. Do not start a frontend comparison in this skill. Field validation against the migrated tree is the `proceso-field-validation` skill, and it requires this `legacy.md`.
