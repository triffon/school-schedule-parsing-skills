---
name: ministry-of-education-and-science/non-school-days
publisher: Ministry of Education and Science
artifact: The annual ministerial order setting the organisation of the school year, published each spring for the year that follows
document: nonSchoolDays
schemaVersion: "1"
---

# Ministry of Education and Science: vacations and non-attendance days

> **This is a skeleton example.** The structure below is plausible but has not been checked against a real order — verify every landmark against the actual document before relying on this Skill, and correct it here when it turns out to be wrong. It is committed as a starting point, not as a finished reading.

## The Source

The same ministerial order that fixes the Term, read for a different purpose: the vacations and the individual non-attendance days scattered through the year. `ministry-of-education-and-science/term` reads the boundaries; this Skill reads what falls inside them.

Of the two, this is the one that changes. An amendment adding a non-attendance day arrives mid-year, and re-parsing this document alone is exactly why the Intake is four files rather than one.

## Reading it

Each entry is a labelled range of dates: `label`, `start`, `end`, the last two as `YYYY-MM-DD`. A single day is a range whose `start` and `end` are equal.

The `label` is what an operator sees when reviewing the diff, so keep the order's own wording — "Коледна ваканция", "неучебен ден" — rather than paraphrasing it. It is not published to the calendar; the calendar only sees the dates, as exclusions from each recurring event.

## Quirks

**Only what falls inside the Term.** Semantic validation rejects a range outside the Term, and the order covers the whole school year. When publishing the second term, the autumn vacation is not a Non-school day — it is simply not in this Intake. A vacation straddling the boundary is clipped to the part inside.

**Weekends are not Non-school days.** The Timetable has no Saturday or Sunday Lessons, so a weekend excludes nothing and only adds noise to the diff. Record the school-week days. A vacation range spanning a weekend is still one range — do not split it.

**Vacations differ by grade.** Some vacations apply to particular grades only; the order says which. Take the ones that apply to the Class's grade, and no others.

**A day off is not always a vacation.** The order names individual non-attendance days — a national holiday falling in term, a day given over to elections or to a school event. Each is its own one-day range. These are the entries an amendment most often adds late.

**Dates are `DD.MM.YYYY г.`, inclusive.** As in the Term Skill: day first, and a range stated `01.11.2025 г. – 03.11.2025 г. вкл.` ends *on* the third, so `end` is `2025-11-03` rather than the day after.

**A makeup Saturday is out of scope.** When a holiday is compensated by teaching on a Saturday, that Saturday is not a Non-school day and this pipeline cannot express it as a teaching day either. Leave it out and handle it by hand.

## Worked example

Given, for a Term running 2026-02-05 to 2026-06-30:

```
Коледна ваканция — 24.12.2025 г. – 04.01.2026 г. вкл.
Междусрочна ваканция — 03.02.2026 г. – 04.02.2026 г. вкл.
Пролетна ваканция (I – XI клас) — 02.04.2026 г. – 12.04.2026 г. вкл.
06.05.2026 г. — неучебен ден
```

produce:

```json
{
  "schemaVersion": "1",
  "ranges": [
    { "label": "Пролетна ваканция", "start": "2026-04-02", "end": "2026-04-12" },
    { "label": "неучебен ден",      "start": "2026-05-06", "end": "2026-05-06" }
  ]
}
```

The Christmas and mid-term vacations both fall before this Term begins and are dropped — including the mid-term one, which ends the day before the Term starts and is the easiest of the four to include by reflex.
