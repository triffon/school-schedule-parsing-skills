---
name: ministry-of-education-and-science/term
publisher: Ministry of Education and Science
artifact: The annual ministerial order setting the organisation of the school year, published each spring for the year that follows
document: term
schemaVersion: "1"
---

# Ministry of Education and Science: the school-year order

> **This is a skeleton example.** The structure below is plausible but has not been checked against a real order — verify every landmark against the actual document before relying on this Skill, and correct it here when it turns out to be wrong. It is committed as a starting point, not as a finished reading.

## The Source

A ministerial order (заповед) fixing the organisation of the school year: when each term begins and ends, how long it runs for each stage of education, and which days are vacations. One document covers the whole country, which is why this Skill lives in the shared library rather than in any one school's data repository — every operator here reads the same artifact.

The same order is also the Source for the Non-school days, read by `ministry-of-education-and-science/non-school-days`. Two Skills over one artifact, because the two Intake documents are re-parsed on different occasions: a term boundary is fixed in spring, while the holiday list is what an amendment later in the year is most likely to touch.

Amendments are published separately as their own orders and do not restate the parts they leave alone. When one exists, the effective content is the original as amended, and the `source` recorded in provenance should name whichever document the value actually came from.

## Reading it

The Term is a single `start` and `end`, both `YYYY-MM-DD`.

`start` is the first day of classes for the term being published. `end` is the last day of classes — **not** the last day of the school year, and not the end of the examination period that follows it.

A school year with two terms is two separate Intakes, published in two runs against two data repositories or two branches. This Skill produces one of them; the operator says which.

## Quirks

**The last day of classes depends on the grade.** The order gives different end dates per stage of education, because younger grades finish earlier. Read the row for the Class's own grade, not the first row in the table and not the latest date in it. Getting this wrong produces a Term that is valid, publishes cleanly, and quietly carries lessons for weeks after the class has broken up.

**Dates are written `DD.MM.YYYY г.`** — day first, with a trailing `г.` for "година". Convert to ISO; `03.04.2026` is the third of April, never the fourth of March.

**"Включително" is load-bearing.** Ranges in the order are stated inclusive of both endpoints, and the Term's `end` is likewise the last day on which lessons happen, not the day after. Recurrence in the calendar is bounded by that date inclusively.

**The term boundary is stated once, in prose, not in the table.** The tables cover vacations and stage end-dates; the sentence naming the start of the second term is usually in the numbered paragraphs above them. Do not infer the start from the end of the winter vacation — an amendment can move one without the other.

## Worked example

Given, for a class in the stage whose classes end on 30 June:

```
1. Начало на учебната година — 15.09.2025 г.
2. Начало на втория учебен срок — 05.02.2026 г.
3. Край на учебните занятия:
   … V – VI клас — 30.06.2026 г. включително
```

and an operator publishing the second term, produce:

```json
{
  "schemaVersion": "1",
  "start": "2026-02-05",
  "end": "2026-06-30"
}
```

Publishing the first term instead would give `start` of `2025-09-15` and an `end` read from the winter vacation's first day minus one — stated elsewhere in the order, and worth quoting here once it has been confirmed.
