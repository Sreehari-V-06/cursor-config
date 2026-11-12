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

Paths under **Where to change** start at the migrated tree. Name the file, the field or method, and the change. Write `No change` on a Match row.

| Fix | Tab | Section | Item | Kind | Behavior | Old page | New page | Verdict | Where to change |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `- [ ]`, or empty when Match | `<tab on the capture screen>` | `<section heading on that tab>` | `<full label on the screen>` | `<Text / Field>` | `<Shown / Hidden / Enabled / Disabled / Readonly / Wording>` | `<when, in plain words>` | `<when, in plain words>` | `<Match / Incorrect>` | `<file, field, and the change, or No change>` |

One sentence on the fields that stay visible and editable, and the headings that keep the same words. If this process has no show, hide, enable, disable, readonly, or wording branch: `None`.

## Corrected

Fixed since the previous pass. On the first run write `None`.

| Tab | Section | Item | Was | What was wrong | What it does now | Where to change |
| --- | --- | --- | --- | --- | --- | --- |
| `<tab>` | `<section heading>` | `<full label on the screen>` | `<Incorrect / Incomplete / Missing>` | `<previous mismatch, in plain words>` | `<the rule the code now enforces>` | `<file and field that now hold the fix>` |

## Incorrect

The new page still disagrees with the old page. Check **Fix** on a row to include it in the next fix.

### <short group name>

| Fix | Tab | Section | Item | Old page | New page | Example | Where to change |
| --- | --- | --- | --- | --- | --- | --- | --- |
| - [ ] | `<tab>` | `<section heading>` | `<full label on the screen>` | `<what the old page does>` | `<what the new page does>` | `<one value>` | `<file inside this process, the field or method, and the change>` |

If none: `None`.

## Missing

The old page checks this and the new page has no equivalent.

| Fix | Tab | Section | Item | Old page | Where to change |
| --- | --- | --- | --- | --- | --- |
| - [ ] | `<tab>` | `<section heading>` | `<full label on the screen>` | `<rule, in plain words>` | `<file inside this process, the field or method, and the change. Name a shared file only when this form cannot do it alone>` |

If none: `None`.

## Incomplete

Part of the rule is present. The same values still pass and fail.

| Fix | Tab | Section | Item | Already there | Still absent | Where to change |
| --- | --- | --- | --- | --- | --- | --- |
| - [ ] | `<tab>` | `<section heading>` | `<full label on the screen>` | `<what matches>` | `<what is absent>` | `<file inside this process, the field or method, and the change>` |

If none: `None`.

## Match

| Tab | Section | Item | What matches |
| --- | --- | --- | --- |
| `<tab>` | `<section heading>` | `<full label on the screen>` | `<required, max, pattern, or behavior, in plain words>` |

## Behavior

A short map of the tables above. One line each. Do not repeat the file and the change.

- `<what the person sees>` — `<Match / Incorrect / Incomplete / Missing / Corrected>` — `<which section>`

## Only in the migrated form

Controls on the new page that the old page does not have. Not a gap.

| Tab | Section | Control | Checks found | Where it lives |
| --- | --- | --- | --- | --- | --- |
| `<tab>` | `<section heading>` | `<name>` | `<required, pattern, max>` | `<file>. No change.` |

If none: `None`.

## Correctly absent

Old ids that are not on the capture page. The new form correctly ignores them.

- `<selector>`

If none: `None`.

## Not compared

Looked for in the old page and not found. Not scored.

- `<field>` — `<what was searched>` — `<file>`
