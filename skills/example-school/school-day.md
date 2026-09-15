---
name: example-school/school-day
publisher: Example School
artifact: The bell schedule published as a single-page PDF on the school's website, unchanged from year to year
document: schoolDay
schemaVersion: "1"
---

# Example School: the bell schedule

> **This is a skeleton example.** Example School is fictional, and this file exists to show the shape of a Skill rather than to parse anything real. Copy it, do not cite it.

## The Source

A one-page PDF titled "Дневен режим", linked from the school's front page under "За ученика". It is republished only when the school changes its bell times, which is rarely — an operator who already has a School day Intake usually does not need to re-parse this.

It is not the *weekly timetable*, which is a separate artifact covered by `example-school/timetable`. The two look nothing alike, but both are sometimes filed under "Разписание" on the site.

## Reading it

A single two-column table. The left column holds a slot number, the right a time range written as `HH:MM – HH:MM` with an en dash. Rows are in chronological order, first slot of the day at the top.

Break rows are interleaved, but they carry no number in the left column — instead the left cell is either empty or holds the break's name. That is the only thing distinguishing a break row from a slot row.

Each numbered row becomes a `slot` entry in `sequence`, in the order read. Each break row becomes a `break` entry, with `label` set to the left cell's text where it has one and omitted where it is empty.

## Quirks

**Not every gap has a row.** The table only shows a break row for the long breaks the school names. The short turnarounds between consecutive lessons are left implicit — row 1 ends at 08:40 and row 2 begins at 08:50 with nothing between them. The School day must be one unbroken run of time, so every such gap needs a `break` entry synthesised to fill it, with no `label`. An Intake that omits them fails semantic validation, naming the minutes nothing accounts for.

**Slot numbers are not recorded.** The left column numbers the slots 1, 2, 3…, but a Slot in the Intake carries no number — it is identified by its position among the Slots, counting from 1 and ignoring Breaks. Emit the entries in order and drop the numbers. They matter only as a check: if the numbers in the artifact skip or repeat, the reading is wrong.

**The dash is an en dash, and sometimes a hyphen.** Older revisions of the PDF use `-`. Split on either.

**A trailing note is not a row.** The table is followed by a line about the after-school group, set in the same face and easily picked up as a further break row. It is not part of the School day; the pipeline publishes lessons only.

## Worked example

Given:

```
1.                 08:00 – 08:40
2.                 08:50 – 09:30
голямо междучасие  09:30 – 09:50
3.                 09:50 – 10:30
```

Produce:

```json
{
  "schemaVersion": "1",
  "sequence": [
    { "kind": "slot",  "start": "08:00", "end": "08:40" },
    { "kind": "break", "start": "08:40", "end": "08:50" },
    { "kind": "slot",  "start": "08:50", "end": "09:30" },
    { "kind": "break", "start": "09:30", "end": "09:50", "label": "голямо междучасие" },
    { "kind": "slot",  "start": "09:50", "end": "10:30" }
  ]
}
```

The 08:40–08:50 break appears in no row of the artifact. It is there because the School day may not have holes.
