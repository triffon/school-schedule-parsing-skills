---
name: ministry-of-education-and-science/term
publisher: Ministry of Education and Science
artifact: The annual ministerial order (заповед РД09-…) fixing the organisation of the school year, issued at the end of August for the year that begins the following month
document: term
schemaVersion: "1"
---

# Ministry of Education and Science: the school-year order

## The Source

A two-page order — `ЗАПОВЕД`, on the letterhead of the Министър на образованието и науката — issued under чл. 104 от Закона за предучилищното и училищното образование and identified by a registration number of the form `РД09-3345/31.08.2026 г.` It fixes, for one school year and for the whole country, the vacations, the individual days off, and the start and end of the **second** срок. One document serves every school, which is why this Skill lives in the shared library rather than in any one school's data repository.

Schools republish it on their own sites, often renamed — `4-zapoved_grafik-na-uchebno-vreme-2026-2027-31082026.pdf` is one school's copy of the 2026/27 order. The copy is the same document; parse it.

The same order is the Source for the Non-school days, read by `ministry-of-education-and-science/non-school-days`. Two Skills over one artifact, because a term boundary is settled once and a later amendment is far likelier to touch the day-off list.

Amendments are published as their own orders and do not restate the parts they leave alone. When one exists, the effective content is the original as amended, and `provenance.source` should name whichever document the value actually came from.

## Reading it

Below `О П Р Е Д Е Л Я М:` and the line `За учебната 2026 – 2027 година:` come four numbered items. Two of them are this document's:

**`start`** — item **3**, `Начало на втория учебен срок`, a two-column list of a date against a grade range. In the 2026/27 order there is a single row, `03.02.2027 г. / I – ХIІ клас`.

**`end`** — item **4**, `Край на втория учебен срок`, the same two columns with one row per stage of education. Take the row whose grade range contains the Class, and if two rows do, the narrower one — see the quirks.

Dates are `DD.MM.YYYY г.`, day first. Items 1 and 2 belong to the Non-school days Skill.

## Quirks

**The order does not give the first срок.** It states the start and the end of the *second* срок and nothing else: no `начало на учебната година`, no `край на първия учебен срок`. An operator publishing срок I gets nothing from this artifact and must read the school's own calendar of teaching weeks instead. This was the skeleton's worst guess — it invented a `Начало на учебната година` item that the real order has never had.

**The end of the срок depends on the grade, and the rows overlap.** The 2026/27 order ends the year on `13.05.2027` for ХІІ, `02.06.2027` for І–III, `16.06.2027` for IV–VІ, `02.07.2027` for V–VІ *в паралелки в спортни училища*, and `02.07.2027` for VII–ХІ. A fifth-grader is covered by both the IV–VІ row and the sports row; the sports row is a narrower qualification and applies only to a sports school. Reading the first matching row, or the latest date in the item, produces a Term that is valid, publishes cleanly, and quietly carries Lessons for weeks after the class has broken up.

**The last row carries a parenthesis that is not a date range.** `VII – ХІ клас (в периода 05.07.2027 г. –31.08.2027 г. + 2 седмици за производствена практика …)` is about summer practice for vocational classes. The `end` is `02.07.2027`, in the left column; nothing in the parenthesis is a Term bound.

**Roman numerals mix Cyrillic and Latin letters.** `ХIІ` in item 3 is a Cyrillic `Х`, a Latin `I` and a Cyrillic `І`; `І – III` switches alphabet between the two sides of the dash. Read them as the numerals they look like. Searching the extracted text for `XII` will not find `ХIІ`.

**Dates are `DD.MM.YYYY г.`, and ranges are inclusive.** `03.04.2027` is the third of April, never the fourth of March, and `вкл.` means the range ends *on* the date given. The Term's `end` is likewise the last day on which Lessons happen, and the calendar's recurrence is bounded by it inclusively.

**Page furniture lands in the middle of the text.** Each page carries `класификация на информацията: Ниво 0, [TLP-WHITE]` at the top and an electronic signature block — `Signed by: …`, a registration number, a bare date — that text extraction drops between the numbered items. None of it is content; the stray `31.8.2026 г.` next to the signature is the date of signing, not a date of the school year.

## Worked example

Given, for a Class in V клас at an ordinary school:

```
3. Начало на втория учебен срок:
03.02.2027 г.                     I – ХIІ клас

4. Край на втория учебен срок:
13.05.2027 г.                     ХІІ клас
02.06.2027 г.                     І – III клас
16.06.2027 г.                     IV – VІ клас
02.07.2027 г.                     V – VІ клас (за паралелки в спортни училища)
02.07.2027 г.                     VII – ХІ клас (в периода 05.07.2027 г. –31.08.2027 г. + …)
```

produce:

```json
{
  "schemaVersion": "1",
  "start": "2027-02-03",
  "end": "2027-06-16"
}
```

`16.06.2027` is the IV–VІ row; the `02.07.2027` two rows down covers V–VІ as well, but only in a sports school, and `02.07.2027` is also the answer for VII–ХІ, which makes it the easiest wrong date on the page to pick up.
