# Process <number> — <short name>

Legacy knowledge base. Source of truth for this trámite only. Built from the controller `<ClassName>`.

## Identity

- Process number: `<number>`
- Entity: `<class>` — `@DiscriminatorValue` evidence: `<path>`
- Package: `<path>`
- Registro URL: `<binding>`
- Consulta URL: `<binding or Not found>`
- Session key: `<constant = value>`
- Spring bean (solicitud): `<bean name>` in `<xml path>`
- Module pom base artifact: `<artifact>` — commons tree used: `<path>`

## Controllers

### Registro

- Class: `<class>`
- Path: `<path>`
- Extends: `<parent>`
- Wizard events: `<list>`
- Capture JSP: `<constant>` → `<path>`
- AJAX methods on this class: `<method>` — request params — return value

### Consulta

- Class: `<class or Not found>`
- Path: `<path>`
- What it loads: `<methods>`

## Service and rules

Confirm both links from code before writing them.

- Service interface: `<class>`
- Service implementation: `<class>`
- Spring bean: `<name>`
- Rules class: `<class>`
- Spring bean: `<name>`
- Repository: `<interface>` / `<implementation>`
- Helper: `<class>`

Methods this service declares or overrides (name and one-line effect):

- `<method>` — `<effect>`

Rules class methods this process declares:

- `<method or "none; class body has no methods">`

The process-specific rule that does not live on the rules class:

- `<method, condition, return values, null assumption>` or `Not found`

## Parents and shared beans

Platform contract: `.cursor/knowledge/legacy-base/architecture.md`. Do not restate it.

Pom base artifact: `<artifactId>` — commons tree: `<path>`

Overrides against that chain. One line each. `not overridden` or `overridden — <effect>`.

- `<Parent>::<method>` — `<not overridden or overridden — effect>`

Shared beans this controller or service calls, and the method. Omit beans the architecture file already names unless this process calls them for a reason that file does not cover.

- `<bean>::<method>` — `<why>`

## Do not use

- Sibling process numbers seen nearby: `<ids>`
- Unused imports in the registro controller: `<types>`
- Commons trees that this module’s pom does not depend on: `<paths>`

## Capture flow

Numbered, in call order. Each step names the method and the file.

1. `<step>`

## Role branches

Each branch that shows, hides, or changes `required`. Quote the condition.

- Condition: `<expression>`
  - Shows: `<ids>`
  - Hides: `<ids>`
  - Adds required: `<ids>`
  - Removes required: `<ids>`
  - Other effect (document, report): `<effect>`

If the capture has no such branch: `Not found`.

## Submit validation

- Action that saves or continues: `<button name / wizard event>`
- Tab or panel switch before validation: `<what, or Not found>`
- Hidden-field behavior: `<ignore :hidden or Not found>`
- Function that can block submit: `<name>` — returns `<true/false>` — blocks when `<condition>`
- Functions that always return true or are commented out: `<name>`

## Fields

One row per rendered input, select, or textarea. `Not found` for a cell the code does not define. Cite the file in Evidence.

| UI label | DOM id | Request param | Model path | Required when | Max | Filter | Validator | Message | On failure | Server | Evidence |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `<label>` | `<id>` | `<param posted, or Not found>` | `<property path>` | `Always` / `When <condition>` / `Label asterisk only` / `Not required` | `<n or Not found>` | `<keypress or keyup pattern>` | `<function and regex>` | `<user-visible text>` | `Clears` / `Marks error` / `Alert only` / `Not found` | `<parse, lookup, or store>` | `<path>` |

Catalog rows add the catalog name and `parentSelectName` in Filter. Required when is `Always` only if `class="required"` or JS adds `required`. An asterisk without that class is `Label asterisk only`.

## Dead selectors

JS or JSP ids that are marked required or validated and are not in the capture markup.

| Selector | What the script does | Evidence |
| --- | --- | --- |
| `<id>` | `<adds required / validates>` | `<path>` |

If none: `Not found`.

## Documents

- Rule id: `<REANX... or Not found>`
- Obligatory when: `<condition and return value>`
- Where it is implemented: `<class>::<method>`
- Document names this process’s code lists: `<names or Not found>`

## Persistence

Helper mapping. One row per persisted property the helper or the controller sets.

| Request param | Entity | Property | Transform | Evidence |
| --- | --- | --- | --- | --- |
| `<param>` | `<entity>` | `<property>` | `<none / date pattern / catalog clave>` | `<path>` |

Repository writes and deletes this process calls:

- `<method>` — `<query or effect>`

## Reports

- Bean: `<name>` → `<jasper path>`
- Chosen when: `<condition>`
- Session key the id is stored under: `<key>`

## Not found

Bullets for sections or cells left as `Not found`, each with the file that was searched.
