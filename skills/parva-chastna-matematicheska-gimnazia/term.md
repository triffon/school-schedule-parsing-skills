---
name: parva-chastna-matematicheska-gimnazia/term
publisher: Първа частна математическа гимназия (ПЧМГ)
artifact: "График на учебните седмици" — the one-page table of numbered teaching weeks the director approves each September and publishes on parvamatematicheska.com
document: term
schemaVersion: "1"
---

# ПЧМГ: the graph of teaching weeks

## The Source

A single landscape page headed **График на учебните седмици на ПЧМГ** and the school year, with the director's electronic signature in the top-left corner and a second, blank page carrying only the school's watermark. It is uploaded to the school's WordPress media library under a name like `Учебни-седмици-26-27.pdf` and linked from the school's site at the start of the year.

It is the school's own reading of the ministry's order, narrowed to the grades ПЧМГ teaches (V–XII) and extended with the school's own days off. Both срокове of the year are on the one page, side by side.

This is **not** the ministerial order `4-zapoved_grafik-na-uchebno-vreme-*.pdf`, which the school also republishes and which is read by `ministry-of-education-and-science/term`. Where the two disagree, this artifact is the school's decision and the one to parse — but see the quirk below, because the order carries a term boundary this page does not.

## Reading it

The page is one table split down the middle: **І-ви срок – V- XII клас** on the left, **ІІ-ри срок - V-XII kлас** on the right. Each half has two columns, `Учебна седмица` (a number) and `Период` (a date range `DD.MM – DD.MM.YYYY`), one row per teaching week, numbered 1–18 on the left and 19–36 on the right. Vacation rows are interleaved in the same table, spanning both columns and carrying a name and a range instead of a week number. Below the table sit two boxes of loose text, **Неучебни дни за I – ви срок** and **Неучебни дни за II – ри срок**.

An operator publishes one срок, and says which. The Term is then:

**`start`** — the first date of the срок's first numbered week: week 1 on the left, week 19 on the right.

**`end`** — the date the page declares as that срок's end for the Class's grade:

- For срок I, the line immediately under the left-hand table: `30.01.2027 – край на І-ви учебен срок`.
- For срок II, the `край на учебна година` line in the right-hand box whose grade range contains the Class — `13.05.2027` for XII, `16.06.2027` for V–VI, `02.07` for VII–XI.

The year in the ranges is written only on the second date of each pair, so week 1's `15.09 – 21.09.2026` is two dates in September 2026, and week 14's `17.12 – 04.01.2027` runs across New Year.

## Quirks

**The declared end of срок I is a Saturday.** `30.01.2027` is a Saturday, while the last numbered week, 18, ends on Friday `29.01.2027`. Take the declared date anyway: it is what the page says, and a Term ending on a Saturday publishes exactly the same Lessons as one ending on the Friday, because the Timetable has no Saturday Lessons. The ministry's order, for its part, counts `30.01.2027` as the first day of the mid-term vacation — a third answer, and the reason to read this line from this page rather than reconciling the two.

**The end of срок II is not the end of the week table.** The numbered weeks run to 36 (`29.06 - 02.07.2027`) because that is when the oldest grades finish. A class in V–VI finishes on `16.06.2027`, in the middle of week 34, and XII finishes on `13.05.2027`, in the middle of week 30. Reading `end` off the last row of the table gives a Term weeks too long for every grade but VII–XI, and it validates cleanly.

**The `край на учебна година` lines are in the box of non-school days.** They sit among the неучебни дни, under the same heading, and they are not non-school days — they are this Skill's `end`. `parva-chastna-matematicheska-gimnazia/non-school-days` leaves them out for the same reason.

**The last line of the right-hand box has the wrong year.** `02.07.2026 – край на учебна година VІІ-ХІ клас` means `02.07.2027`; 2026/27 ends in 2027, and week 36 of the same table ends on `02.07.2027`. Correct it silently. A Term `end` of `2026-07-02` would run backwards and be caught, but only after wasting a run.

**Срок I's start is not in the ministerial order.** The order for 2026/27 states the start of срок *II* and the end of срок II, and nothing about the start of the year or the end of срок I. For срок I this page is the only Source there is, which is why a school-specific Skill exists for a document that otherwise looks like a restatement.

**The school's grades are V–XII.** Both headers say so. A grade range on this page never means a grade the school does not teach — `V-XI kлас` in the boxes is the whole school except the leavers, not a subset to be narrowed further.

**Grade numerals mix Cyrillic and Latin letters.** `V-XII kлас` has a Latin `k`; `VІІ-ХІ` has Cyrillic `І` and `Х` next to Latin `V`. Read them as the numerals they look like; matching the text literally will not find them.

## Worked example

Given the left half of the table and the line below it:

```
      І-ви срок – V- XII клас
Учебна      Период
седмица
    1       15.09 – 21.09.2026
    2       23.09 – 29.09.2026
   ...
   17       19.01 – 25.01.2027
   18       26.01 – 29.01.2027
30.01.2027 – край на І-ви учебен срок
```

and an operator publishing срок I, produce:

```json
{
  "schemaVersion": "1",
  "start": "2026-09-15",
  "end": "2027-01-30"
}
```

`start` comes from the first date of week 1 and `end` from the declared boundary, not from week 18's `29.01.2027`.
