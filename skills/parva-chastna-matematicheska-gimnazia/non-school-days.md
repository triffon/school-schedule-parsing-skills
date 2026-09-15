---
name: parva-chastna-matematicheska-gimnazia/non-school-days
publisher: Първа частна математическа гимназия (ПЧМГ)
artifact: "График на учебните седмици" — the one-page table of numbered teaching weeks the director approves each September and publishes on parvamatematicheska.com
document: nonSchoolDays
schemaVersion: "1"
---

# ПЧМГ: vacations and days off in the graph of teaching weeks

## The Source

The same one-page `График на учебните седмици` that `parva-chastna-matematicheska-gimnazia/term` reads for the Term bounds, read here for everything that falls between them. Two Skills over one artifact: the bounds are settled in September and stay put, while a day off is added by the school during the year.

Of the two possible Sources for this document, prefer this one over `ministry-of-education-and-science/non-school-days`. The ministerial order does not know about the school's own days off — the Christmas bazaar, the school's feast day — and this page carries both those and the ministry's, already narrowed to the grades ПЧМГ teaches.

## Reading it

Three places on the page hold non-school days, and all three are needed.

**The vacation rows inside the week table.** They span both columns where a week number would go, and carry a name and an inclusive range: `Есенна ваканция / 31.10 – 02.11.2026 г. вкл.`, `Коледна ваканция / 24.12.2026–03.01.2027 вкл.`, `Пролетна ваканция / 03.04 – 11.04. вкл. – V-XI kлас / 08.04 – 11.04. вкл. – XII kлас`. Each is one range; take the line whose grades contain the Class.

**The two boxes below the table.** `Неучебни дни за I – ви срок` and `Неучебни дни за II – ри срок`, one day per line, written `DD.MM.YYYY` followed by a dash and a reason. A line may name two days joined by `и` — `19.05.2027 и 21.05.2027 – ДЗИ за XII клас` — and that is two one-day ranges, never a range from the first to the second.

**The gaps between consecutive numbered weeks.** Where one week ends and the next does not begin on the following day, the days in between are non-school days that appear in no list on the page. Week 1 ends `21.09.2026` and week 2 begins `23.09.2026`; the missing `22.09.2026` is Independence Day, and nothing else on the page mentions it.

The `label` is what an operator sees in the diff. Use the page's own name for a vacation, the reason text for a day out of the boxes, and the name of the holiday for a day found only in a gap.

Only what falls inside the Term goes in. Both срокове are on the one page, and the срок not being published contributes nothing.

## Quirks

**The numbered weeks are five teaching days long, and that is the check.** A `Период` is not a Monday-to-Sunday week: it is a run of exactly five days on which Lessons happen, so it slides through the week as days are lost, and it stretches across whatever is not taught. Week 1 is `15.09 – 21.09.2026`, Tuesday to the following Monday, because the year opens on a Tuesday. Week 7, `28.10 – 04.11.2026`, is eight days long because the autumn vacation sits inside it. Week 14, `17.12 – 04.01.2027`, is nineteen. Count the Monday-to-Friday days in each range, subtract the non-school days found so far, and the answer must be five — the last week of a срок excepted, which is however short it needs to be. Any week that does not come to five is a non-school day not yet found.

**A gap is the only record of a public holiday.** The page lists what the school decided and what the ministry ordered; it does not list national holidays, because nobody needs telling. In срок I that is `22.09.2026`, Денят на независимостта, visible only as the day between weeks 1 and 2. In срок II the gap between week 29 (`23.04 – 29.04.2027`) and week 30 (`10.05 – 14.05.2027`) is ten days long, of which the boxes account for two: the rest are Good Friday, Easter, Easter Monday, the Monday-to-Tuesday carried over because 1 May falls on a Saturday, and Гергьовден. Read the gaps, not the lists, and use the lists to explain them.

**`заповед МОН` is a citation, not a reason.** Several lines in the boxes — `05.05.2027- V-XI kлас – заповед МОН` — say only that the ministry ordered the day off. Label those `неучебен ден по заповед на МОН` rather than reproducing the citation; the day itself is what matters and the label is only read by a human.

**The `край на учебна година` lines are not days off.** Three lines in the срок II box give the last day of the year for each grade range. They belong to `parva-chastna-matematicheska-gimnazia/term` as its `end`, and recording them here would exclude the last day of teaching from the calendar.

**The mid-term vacation is filed under срок I but falls after it.** `Междусрочна ваканция / На 31.01-02.02.2027 г. – V-XII kлас` sits in the срок I box, below the days off. Срок I ends on `30.01.2027`, so the whole range is outside the Term and none of it is recorded. It is in that box because it follows срок I, not because it is part of it.

**The school and the ministry disagree about `30.01.2027`.** The ministerial order starts the mid-term vacation on the 30th; this page ends срок I on the 30th and starts the vacation on the 31st. Both dates are a weekend, so the disagreement changes nothing that is published — do not try to resolve it, and do not import the order's range over this page's.

**A vacation may begin or end on a weekend.** The autumn vacation is `31.10 – 02.11.2026`, a Saturday to a Monday, of which only the Monday is a teaching day. Record the range the page states, without trimming it to its weekdays: it is one range with one name, and the weekend days it covers exclude nothing.

**Ranges written `DD.MM – DD.MM.YYYY`.** The year appears once, on the second date, and inside the week table sometimes not at all — the spring vacation is `03.04 – 11.04. вкл.`, with the year to be taken from the срок it sits in.

## Worked example

Given, for a Term running `2026-09-15` to `2027-01-30`, these fragments of the left-hand column and the срок I box:

```
    1       15.09 – 21.09.2026
    2       23.09 – 29.09.2026
   ...
    7       28.10 – 04.11.2026
             Есенна ваканция
             31.10 – 02.11.2026 г. вкл.
    8        05.11 – 11.11.2026
   ...
   14       17.12 – 04.01.2027
             Коледна ваканция
             24.12.2026–03.01.2027 вкл.
   15       05.01 – 11.01.2027

Неучебни дни за I – ви срок:
23.12.2026 г. – Коледен базар и коледни тържества

Междусрочна ваканция
На 31.01-02.02.2027 г. – V-XII kлас
```

produce:

```json
{
  "schemaVersion": "1",
  "ranges": [
    { "label": "Ден на независимостта", "start": "2026-09-22", "end": "2026-09-22" },
    { "label": "Есенна ваканция", "start": "2026-10-31", "end": "2026-11-02" },
    { "label": "Коледен базар и коледни тържества", "start": "2026-12-23", "end": "2026-12-23" },
    { "label": "Коледна ваканция", "start": "2026-12-24", "end": "2027-01-03" }
  ]
}
```

`2026-09-22` is in no list and comes from the gap between weeks 1 and 2. The mid-term vacation is dropped for falling after the Term. Week 14 checks out: `17.12 – 04.01.2027` holds the weekdays 17, 18, 21, 22, 23, 24, 25, 28, 29, 30, 31 December and 1 and 4 January; remove the bazaar and the Christmas vacation and five are left.
