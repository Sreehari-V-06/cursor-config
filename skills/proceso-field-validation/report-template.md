# Field validation — process <number>

Migrated tree: `<path>`
Legacy baseline: `.cursor/knowledge/procesos/<number>/legacy.md`
Platform contract: `.cursor/knowledge/legacy-base/architecture.md`
Pass: `<1 on the first run, then previous pass + 1>`
Run date: `<date>`

Two or three sentences at the top: what a person can still do on the new page, and what already matches. No file paths in this opening.

To choose a fix, put `x` inside that row’s brackets (`- [x]` or `- [ x]`), save, and run `/proceso-field-fix`. An `x` outside the brackets does not count. The mark is plain Markdown. No HTML and no plugin.

## Counts

| Verdict | Count | Meaning |
| --- | --- | --- |
| Match | `<n>` | The new page blocks the same values as the old page |
| Corrected | `<n>` | This was wrong on an earlier pass and now matches |
| Incorrect | `<n>` | The new page allows a value the old page blocked, or blocks a value the old page allowed |
| Incomplete | `<n>` | Part of the check is present, and the same values still pass and fail |
| Missing | `<n>` | The old page has this check and the new page does not |

## UI behavior

What stays on screen, what is hidden, what can be typed, and which headings change. One row per item. The seven rows that only repeat an Incorrect finding are not counted again.

| Fix | Item | Kind | Behavior | Old page | New page | Verdict |
| --- | --- | --- | --- | --- | --- | --- |
| `- [ ]`, or empty when Match | `<field, heading, or label>` | `<Text / Field>` | `<Shown / Hidden / Enabled / Disabled / Readonly / Wording>` | `<when, in plain words>` | `<when, in plain words>` | `<Match / Incorrect>` |

One sentence on the fields that stay visible and editable, and the headings that keep the same words. If this process has no show, hide, enable, disable, readonly, or wording branch: `None`.

## Corrected

Fixed since the previous pass. On the first run write `None`.

| Item | Was | What was wrong | What it does now | Evidence |
| --- | --- | --- | --- | --- |
| `<field or behavior>` | `<Incorrect / Incomplete / Missing>` | `<previous mismatch, in plain words>` | `<the rule the code now enforces>` | `<migrated path>` |

## Incorrect

The new page still disagrees with the old page. Check **Fix** on a row to include it in the next fix.

### <short group name>

| Fix | Item | Old page | New page | Example | Evidence |
| --- | --- | --- | --- | --- | --- |
| - [ ] | `<field or behavior>` | `<what the old page does>` | `<what the new page does>` | `<one value>` | `legacy.md` row; `<migrated path>` |

If none: `None`.

## Missing

The old page checks this and the new page has no equivalent.

| Fix | Item | Old page | Evidence |
| --- | --- | --- | --- |
| - [ ] | `<field or behavior>` | `<rule, in plain words>` | `legacy.md` row; `<migrated path searched>` |

If none: `None`.

## Incomplete

Part of the rule is present. The same values still pass and fail.

| Fix | Item | Already there | Still absent | Evidence |
| --- | --- | --- | --- | --- |
| - [ ] | `<field or behavior>` | `<what matches>` | `<what is absent>` | `legacy.md` row; `<migrated path>` |

If none: `None`.

## Match

| Item | What matches |
| --- | --- |
| `<field or behavior>` | `<required, max, pattern, or behavior, in plain words>` |

## Behavior

A short map of the tables above. One line each. Do not repeat the full evidence.

- `<what the person sees>` — `<Match / Incorrect / Incomplete / Missing / Corrected>` — `<which section>`

## Only in the migrated form

Controls on the new page that the old page does not have. Not a gap.

| Control | Checks found | Evidence |
| --- | --- | --- |
| `<name>` | `<required, pattern, max>` | `<path>` |

If none: `None`.

## Correctly absent

Old ids that are not on the capture page. The new form correctly ignores them.

- `<selector>`

If none: `None`.

## Not compared

Looked for in the old page and not found. Not scored.

- `<field>` — `<what was searched>` — `<file>`
