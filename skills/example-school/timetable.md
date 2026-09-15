---
name: example-school/timetable
publisher: Example School
artifact: The weekly timetable grid published as a PDF per class each September, and reissued mid-year when staffing changes
document: timetable
schemaVersion: "1"
---

# Example School: the weekly timetable

> **This is a skeleton example.** Example School is fictional, and this file exists to show the shape of a Skill rather than to parse anything real. Copy it, do not cite it.

## The Source

One PDF per class, named after the class — `5b.pdf` — published under "Седмично разписание" in September and reissued whenever staffing changes, usually once around the start of the second term. Check the date in the footer against the Term being published; an operator reaching for last year's file is the common mistake.

It is not the bell schedule, which is `example-school/school-day`, and it is not the whole-school grid that the same page sometimes carries as a single wide PDF. This Skill reads the per-class file.

## Reading it

A grid. Weekdays run across the top as column headers, Monday first. Slot numbers run down the left, matching the numbering in the bell schedule. Each cell holds a subject, or is empty.

Every non-empty cell is one `lesson`: `weekday` from the column header translated to the English lower-case name the schema requires, `slot` from the row's number, `subject` from the cell. Empty cells produce nothing — ragged days are normal and a short Friday is not an error.

The class name in the header is not part of the Intake. It belongs in the data repository's Config, where it is used as a display string.

## Quirks

**Abbreviations, expanded from the legend.** Cells hold short forms — `БЕЛ`, `ФВС`, `ЧП` — with a legend below the grid giving each in full. Record the **expansion**, not the abbreviation. The subject is published verbatim as an event title and a sheet cell, and a student reading their calendar should see the subject as it is spoken, not as it is abbreviated to fit a cell. This is a judgement rather than a rule the artifact states, and it is written down here so that re-parsing next year produces the same strings and the git diff stays empty.

**A slash means a group split.** `АЕ/НЕ` in one cell means the class divides, half to one subject and half to the other. The pipeline has no concept of groups: one Class, one Timetable, one subject per cell. Record the subject *this* Class's student actually attends, which the artifact does not say — ask the operator rather than guessing, and write the answer into the data repository's own copy of this Skill so the next parse does not ask again.

**A Saturday column may appear, and is ignored.** When a national holiday is made up on a Saturday, the reissued PDF gains a sixth column for that one week. Moved school days are out of scope; drop the column. The Intake describes the recurring week.

**A repeated subject on the same day is not always a double period.** Consecutive identical cells are merged into one Block by the pipeline, automatically. Non-consecutive ones are not, and must not be made so. Record cells, never runs — the merging is not this Skill's job and doing it by hand produces an Intake the pipeline cannot reason about.

**The header row repeats on page two.** Long timetables spill onto a second page which restates the weekday headers. That is not a slot row; the numbering in the left column is what continues.

## Worked example

Given a fragment of the grid — rows 1 and 2, with Wednesday empty in row 2:

```
      Понеделник   Вторник   Сряда   Четвъртък   Петък
1.    БЕЛ          МАТ       БЕЛ     ФВС         МАТ
2.    БЕЛ          АЕ/НЕ             МАТ         ЧП

БЕЛ — Български език и литература    МАТ — Математика
ФВС — Физическо възпитание и спорт   ЧП  — Човекът и природата
АЕ  — Английски език                 НЕ  — Немски език
```

with the operator confirming this Class takes Английски език, produce:

```json
{
  "schemaVersion": "1",
  "lessons": [
    { "weekday": "monday",    "slot": 1, "subject": "Български език и литература" },
    { "weekday": "monday",    "slot": 2, "subject": "Български език и литература" },
    { "weekday": "tuesday",   "slot": 1, "subject": "Математика" },
    { "weekday": "tuesday",   "slot": 2, "subject": "Английски език" },
    { "weekday": "wednesday", "slot": 1, "subject": "Български език и литература" },
    { "weekday": "thursday",  "slot": 1, "subject": "Физическо възпитание и спорт" },
    { "weekday": "thursday",  "slot": 2, "subject": "Математика" },
    { "weekday": "friday",    "slot": 1, "subject": "Математика" },
    { "weekday": "friday",    "slot": 2, "subject": "Човекът и природата" }
  ]
}
```

Monday's two Български език и литература cells stay two Lessons. The pipeline will publish them as one Block; the Intake does not say so.
