---
name: ministry-of-education-and-science/non-school-days
publisher: Ministry of Education and Science
artifact: The annual ministerial order (заповед РД09-…) fixing the organisation of the school year, issued at the end of August for the year that begins the following month
document: nonSchoolDays
schemaVersion: "1"
---

# Ministry of Education and Science: vacations and non-attendance days

## The Source

The same ministerial order that fixes the second срок, read for a different purpose: the vacations in item 1 and the individual non-attendance days in item 2. `ministry-of-education-and-science/term` reads the boundaries; this Skill reads what falls inside them.

Of the two, this is the one that changes. An amendment adding a day off arrives mid-year, and re-parsing this document alone is exactly why the Intake is four files rather than one.

This is the Source of last resort. A school that publishes its own calendar of teaching weeks publishes a better one — it has already narrowed the grade ranges to the grades it teaches, and it adds the days off the school itself declares, which are invisible here. Use this Skill when the school publishes nothing of its own, or to check what the school published.

## Reading it

Each entry is a labelled range of dates: `label`, `start`, `end`, the last two as `YYYY-MM-DD`. A single day is a range whose `start` and `end` are equal.

**Item 1, `Начало и край на ваканциите (с включени празнични и почивни дни) с изключение на лятната`** — one row per vacation, an inclusive range in the left column and the vacation's name in the right, lower-case and bare: `31.10.2026 г. – 02.11.2026 г. вкл. / есенна`. Capitalise it into a label — `Есенна ваканция`. A vacation split by grade gets one row per grade range; take the row that covers the Class.

**Item 2, `Неучебни дни`** — one row per day, a date in the left column and the reason in the right, running to several lines. The reason is the label; shorten it to its first clause, because the whole of `изпит по български език и литература от националното външно оценяване в края на VII клас и по български език и литература с интегриране на други учебни предмети от националното външно оценяване в края на X клас` is one day off.

Only what falls inside the Term goes in. The order covers a whole school year and an Intake covers one срок.

## Quirks

**The order lists no public holidays.** Item 1's parenthesis, `с включени празнични и почивни дни`, says that the vacations already absorb the holidays they cover — and that is all the order has to say about them. A national holiday that falls in term time outside a vacation appears nowhere: Денят на независимостта on 22 September, Освобождението on 3 March, Гергьовден on 6 May, and the weekdays given in lieu when one of them falls on a weekend. They are still Non-school days. This Source cannot give them to you, so take them from the school's own calendar, or add them knowing what they are.

**A row may hold two dates joined by `и`, and they are not a range.** `05.05. и 07.05.2027 г.` is two one-day ranges, the fifth and the seventh. The sixth is missing from the row because it is Гергьовден, a public holiday the order does not list — so in practice the three run together, but only two of them are written here. Read `и` as "and", never as a dash.

**The first of two dates omits its year, and sometimes its month's trailing dot doubles as the separator.** `05.05. и 07.05.2027 г.` gives the year once, at the end. Carry it back to the first date.

**Vacations differ by grade.** `03.04.2027 г. – 11.04.2027 г. вкл. пролетна за І – ХI клас` and `08.04.2027 г. – 11.04.2027 г. вкл. пролетна за XII клас` are the same vacation at two lengths. Take the row that covers the Class's grade and no other; a leaver's spring vacation is four days, not nine.

**Days off in item 2 carry a grade only where the order says so.** `05.05. и 07.05.2027 г.` is marked `за І – ХI клас`; the exam days `19.05`, `21.05`, `18.06` and `21.06` are not marked at all, and are days off for everyone, not only for the grade sitting the exam. Do not narrow an unmarked row to the grades its reason mentions.

**Only what falls inside the Term.** Semantic validation rejects a range outside it. When publishing срок II, the autumn and Christmas vacations are not Non-school days — they are simply not in this Intake. A vacation straddling the boundary is clipped to the part inside, and if what is left holds no Monday-to-Friday day, drop it: the mid-term vacation runs `30.01.2027 г. – 02.02.2027 г.`, and against a срок I that ends on the 30th, a Saturday, the clipped remainder is one weekend day that excludes nothing.

**Weekends are not Non-school days of their own.** The Timetable has no Saturday or Sunday Lessons, so a weekend excludes nothing and only adds noise to the diff. A vacation that spans or begins on a weekend is still one range, recorded as the order states it — do not split it and do not trim it.

**Dates are `DD.MM.YYYY г.`, inclusive.** Day first, and a range stated `31.10.2026 г. – 02.11.2026 г. вкл.` ends *on* the second, not the day before or the day after.

**A makeup Saturday is out of scope.** When a holiday is compensated by teaching on a Saturday, that Saturday is not a Non-school day and this pipeline cannot express it as a teaching day either. Leave it out and handle it by hand.

**Page furniture lands in the middle of item 2.** The page break falls inside the list of days off, and the header `класификация на информацията: Ниво 0, [TLP-WHITE]` is extracted between two rows. Skip it; the row it interrupts continues below.

## Worked example

Given, for a Term running `2027-02-03` to `2027-06-16` and a Class in V клас:

```
1. Начало и край на ваканциите (с включени празнични и почивни дни) с изключение
на лятната:
31.10.2026 г. – 02.11.2026 г. вкл.    есенна
30.01.2027 г. – 02.02.2027 г. вкл.    междусрочна
03.04.2027 г. – 11.04.2027 г. вкл.    пролетна за І – ХI клас
08.04.2027 г. – 11.04.2027 г. вкл.    пролетна за XII клас

2. Неучебни дни:
05.05. и 07.05.2027 г.    за І – ХI клас във връзка с Великден и с Деня на
                          храбростта и Българската армия
19.05.2027 г.             задължителен държавен зрелостен изпит по български
                          език и литература
```

produce:

```json
{
  "schemaVersion": "1",
  "ranges": [
    { "label": "Пролетна ваканция", "start": "2027-04-03", "end": "2027-04-11" },
    { "label": "Великден и Ден на храбростта", "start": "2027-05-05", "end": "2027-05-05" },
    { "label": "Великден и Ден на храбростта", "start": "2027-05-07", "end": "2027-05-07" },
    { "label": "Държавен зрелостен изпит", "start": "2027-05-19", "end": "2027-05-19" }
  ]
}
```

The autumn vacation falls before this Term and the mid-term one ends the day before it starts — the easier of the two to include by reflex. The spring vacation is the І–ХI row, not the XII one. `05.05` and `07.05` are two separate days. `06.05.2027`, Гергьовден, is a Non-school day that this artifact does not mention at all, and it is missing from the JSON above for exactly that reason.
