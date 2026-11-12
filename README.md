# VUCEM Cursor setup

This folder is the shared Cursor setup for everyone on this project, backend and frontend. The same `.cursor` is cloned into each developer's parent folder. That parent is the folder opened in Cursor. Its name can be anything. Two things inside it are required: `.cursor/` and the old code. The old code is `old/`, `legacy/`, or `old code/`. Letter case does not matter. A developer needs only one of those.

The new form and the new backend are not part of that pair. A developer may have neither. When they are present, they may sit in a folder named `new code/`, or at another path in the parent.

## Initial setup

Clone the shared Cursor configuration repository inside your project’s parent folder:

git clone https://github.com/Sreehari-V-06/cursor-config.git .cursor

Important: Run this command from the parent folder where you want to work. It automatically creates the .cursor folder containing the shared rules, knowledge, and skills.
Open the parent folder in Cursor. The parent folder can have any name.

Every developer opens a parent folder like this:

```
<parent folder>/
  .cursor/                              required
  old/   or  legacy/   or  old code/    required, one of these
  new code/                             optional
```

If none of `old/`, `legacy/`, or `old code/` is there, stop. Say the parent must contain `.cursor/` and the old code in `old/`, `legacy/`, or `old code/`. Do not look for the old code anywhere else in the parent.

`/legacy-proceso-knowledge-base` reads that old code. It does not need the new form or the new backend.

The form commands also need the new form folder for that process. One example is `Project/frontend/apps/agace/src/app/application/tramites/32515/`. Another machine keeps that form under `new code/`. Those commands look for `tramites/<process number>` inside the parent, including inside `new code/`.

`/github-ticket-fix-backend` reads the old Java inside `old/`, `legacy/`, or `old code/`, and the new backend module for that process. A folder named `ms-sat-t<process number>` is one example of the new module. It may sit under `new code/`. It does not need the form.

When a search finds one match, use it. When it finds more than one, ask which path. When it finds none, ask for the path.

## Commands

`/legacy-proceso-knowledge-base` is for anyone who has the old code. The form commands are for the frontend. `/github-ticket-fix-backend` is for the backend. A command does not look for a tree it does not use.

### Shared

#### `/legacy-proceso-knowledge-base`

Builds or corrects the notes for one process from the old code in `old/`, `legacy/`, or `old code/`. Anyone with that folder can run it. The new form and the new backend may be absent. When they are present, they may sit under `new code/`.

The notes are a map to the correct old file and method. The old tree has many similar classes, and a search can open the wrong one. Later commands open the files named in that map. They do not search the whole old tree.

It asks for the process number, then the old controller class or path. It writes `.cursor/knowledge/procesos/<process number>/legacy.md`. It does not edit Java, JSP, or JS. Run it again to correct a wrong file or to add a behavior the map does not list yet. A later command may add one confirmed row. It does not replace the rest of the file.

### Frontend

Run these two from the Cursor chat, in this order. Each asks for the process number when the message does not include one. Run `/legacy-proceso-knowledge-base` first when that process has no `legacy.md`.

#### `/proceso-field-validation`

Compares those notes with the new form. It does not edit the form. It does not fix a backend issue. That command is `/github-ticket-fix-backend`.

It writes `model-result/<process number>/filed-validation/report.md` in the open folder. Run it again after a fix: it updates that same file and moves a fixed row to **Corrected**.

The report counts are:

| Verdict | Meaning |
| --- | --- |
| Match | The new page blocks the same values as the old page |
| Corrected | This was wrong before and now matches |
| Incorrect | The new page allows a value the old page blocked, or blocks a value the old page allowed |
| Incomplete | Part of the check is present, and the same values still pass and fail |
| Missing | The old page has this check and the new page does not |

#### `/proceso-field-fix`

Edits only the rows you marked in that report. Run validation first. If the report is missing, this command stops. This is the form fix. A backend GitHub issue stays on `/github-ticket-fix-backend`.

### Backend

#### `/github-ticket-fix-backend`

Fixes one GitHub issue in the backend only. Give the issue URL or number in the message. If that is missing, the command asks and stops. A form check stays on `/proceso-field-validation`. A form fix stays on `/proceso-field-fix`. The frontend trees may be absent. This command does not look for them.

It reads the issue, takes the process number, reads `.cursor/knowledge/legacy-base/architecture.md` and `.cursor/knowledge/procesos/<process number>/legacy.md` when that file exists, then compares the old Java inside `old/`, `legacy/`, or `old code/` with the new backend module for that process. That module may sit under `new code/`. It edits only that new module. It does not edit the form, the old code, a sibling process, or a shared service unless you choose **Allow** for a shared backend module. It does not open a pull request or commit. If the old Java or the new module is missing, or more than one folder matches, it asks for the path.

## How to choose rows

In the report, the first column of **Incorrect**, **Missing**, **Incomplete**, and **UI behavior** is **Fix**. An open row looks like `- [ ]`. A row that already matches has an empty Fix cell.

In the editor, put `x` inside the brackets and save the file. These all count: `- [x]`, `- [X]`, `- [ x]`, `- [ x ]`, `[x]`, `[ x]`. An `x` written outside the brackets does not count. A click in the preview does not write the mark into a table cell.

Then run `/proceso-field-fix` and give the process number.

## What a fix is allowed to change

A fix is written only inside that process’s form folder. `Project/frontend/apps/agace/src/app/application/tramites/32515/` is one example. The same limit applies to every process in every department, wherever that folder sits.

The command does not edit shared pieces such as `formas-dinamicas`, `input-fecha`, catalog selects, or `libs/shared`. It does not edit the old Java, JSP, or JS, and it does not edit another process.

Before it edits a file outside the process folder, it asks **Allow** or **Do not allow** and waits. Before it removes an existing validation, show/hide behavior, component, heading, label, or message, it asks the same way and waits. Adding or tightening a rule so it matches the old page does not need that extra permission.

The report is not rewritten during the fix. The next `/proceso-field-validation` pass records what now matches.

## Shared copy

The Git repository for this setup contains `.cursor` only. The old code, the new code, and `model-result/` stay on your machine. Pull to receive updates. A local edit of `.cursor` is not the shared copy unless it is reviewed and merged.
