# VUCEM field check

This folder is the shared Cursor setup for comparing one trámite’s old page with its new form. Open the parent folder in Cursor. The parent folder can have any name. These paths have to sit inside it:

```
<open folder>/
  .cursor/                      ← this setup
  legacy/vuc_mtto_aga2/         ← old process modules
  legacy/vuc_mtto_commons/      ← old shared platform
  Project/frontend/             ← new form
```

A trámite in the new form lives at `Project/frontend/**/tramites/<process number>/`. For 32515 that is `Project/frontend/apps/agace/src/app/application/tramites/32515/`. Another department uses its own app folder under the same `Project/frontend`.

If the old code or the new code already lives somewhere else, link it in under these names. On Windows:

```
mklink /J legacy D:\src\old-vucem
mklink /J Project D:\src\new-code
```

`Project\frontend` has to exist inside the new-code tree. The commands look only inside the open folder. They stop and name any path that is missing.

## Commands

Run them from the Cursor chat, in this order. Each command asks for the process number when the message does not include one.

### 1. `/legacy-proceso-knowledge-base`

Builds the notes for one process from the old code. It asks for the process number, then the old controller class or path.

It writes `.cursor/knowledge/procesos/<process number>/legacy.md`. It does not edit Java, JSP, or JS. Skip this command when that file is already in the clone.

### 2. `/proceso-field-validation`

Compares those notes with the new form. It does not edit the form.

It writes `model-result/<process number>/filed-validation/report.md` in the open folder. Run it again after a fix: it updates that same file and moves a fixed row to **Corrected**.

The report counts are:

| Verdict | Meaning |
| --- | --- |
| Match | The new page blocks the same values as the old page |
| Corrected | This was wrong before and now matches |
| Incorrect | The new page allows a value the old page blocked, or blocks a value the old page allowed |
| Incomplete | Part of the check is present, and the same values still pass and fail |
| Missing | The old page has this check and the new page does not |

### 3. `/proceso-field-fix`

Edits only the rows you marked in that report. Run validation first. If the report is missing, this command stops.

## How to choose rows

In the report, the first column of **Incorrect**, **Missing**, **Incomplete**, and **UI behavior** is **Fix**. An open row looks like `- [ ]`. A row that already matches has an empty Fix cell.

In the editor, put `x` inside the brackets and save the file. These all count: `- [x]`, `- [X]`, `- [ x]`, `- [ x ]`, `[x]`, `[ x]`. An `x` written outside the brackets does not count. A click in the preview does not write the mark into a table cell.

Then run `/proceso-field-fix` and give the process number.

## What a fix is allowed to change

A fix is written only inside that process’s folder under `Project/frontend`. The agace path above is one example. The same limit applies to every process in every department.

The command does not edit shared pieces such as `formas-dinamicas`, `input-fecha`, catalog selects, or `libs/shared`. It does not edit the old Java, JSP, or JS, and it does not edit another process.

Before it edits a file outside the process folder, it asks **Allow** or **Do not allow** and waits. Before it removes an existing validation, show/hide behavior, component, heading, label, or message, it asks the same way and waits. Adding or tightening a rule so it matches the old page does not need that extra permission.

The report is not rewritten during the fix. The next `/proceso-field-validation` pass records what now matches.

## Shared copy

The Git repository for this setup contains `.cursor` only. The old code, the new code, and `model-result/` stay on your machine. Pull to receive updates. A local edit of `.cursor` is not the shared copy unless it is reviewed and merged.
