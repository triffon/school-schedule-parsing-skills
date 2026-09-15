---
name: parva-chastna-matematicheska-gimnazia/school-day
publisher: Първа частна математическа гимназия (ПЧМГ)
artifact: "График на учебните часове" — the one-page list of the school's eight lesson times that the director approves each September and publishes on parvamatematicheska.com
document: schoolDay
schemaVersion: "1"
---

# ПЧМГ: the graph of lesson times

## The Source

A single portrait page headed **ГРАФИК / на учебните часове през / учебната 2026/2027 година**, with the school's letterhead and the director's electronic signature block in the top-left corner and the school's π logo watermarked across the middle. It is listed on the school's `Графици за учебната година` page as *График на учебните часове за 2026/2027г.* and uploaded to the WordPress media library as `grafik-y4.4asove-20262027.pdf`.

It is the only place the school states its bell times, and it states them once for the whole year: there is no separate schedule for a shortened day, for a срок, or for a grade. Bell times move rarely, so an operator who already holds a School day Intake usually re-parses this only when the year rolls over.

Four other artifacts on that same page are also called `График`, and none of them is this one:

- `Учебни-седмици-26-27.pdf`, *График за учебните седмици* — the numbered teaching weeks, read by `parva-chastna-matematicheska-gimnazia/term` and `parva-chastna-matematicheska-gimnazia/non-school-days`.
- `4-zapoved_grafik-na-uchebno-vreme-*.pdf` and `6-zapoved-grafik-uons-*.pdf` — the ministerial orders the school republishes, read by the `ministry-of-education-and-science` Skills.
- *График за консултации* and *График за класни и контролни работи* — teachers' consultation hours and test dates. Both are grids of times of day, which is what this document looks like too, and neither is the School day.

The Timetable is not on that page at all: ПЧМГ publishes it through EduPage, at `fpmg.edupage.org/timetable/`, and that is a different Source with a different Skill.

## Reading it

Everything below the title is the artifact. Eight lines, one per Slot, in chronological order, each of the form:

```
N час  -  H.MM  -  H.MM ч.
```

`N` runs 1 to 8. The first time is the Slot's `start`, the second its `end`. Each line becomes one `slot` entry in `sequence`, emitted in the order printed.

No line on the page describes a Break. The Breaks are the gaps between one line's second time and the next line's first, and all seven have to be synthesised — see the quirks, one of which also gives one of them its name.

## Quirks

**The page has no break rows at all.** Unlike a bell schedule that interleaves named breaks, this one lists only the eight lessons, so every Break in the `sequence` is inferred from a gap. The School day may not have holes, so all seven gaps become `break` entries; an Intake that emits eight slots and nothing else fails semantic validation, naming the ten minutes between 8.40 and 8.50 that nothing accounts for.

**The one long gap is the long break, and it is the only Break that gets a label.** After `3 час` the day resumes at 10.40 rather than 10.30: twenty minutes where every other gap is ten, and the school's голямо междучасие. Label that Break `голямо междучасие` and leave the other six unlabelled — a labelled Break renders a row in the sheet, and the long break is worth a row where six identical ten-minute turnarounds are noise.

Take that from the length, not from the position: it is the twenty minutes that make the long break, and a revision that moves it to after `4 час` moves the name with it. Should the artifact ever name a break itself, its name wins over this one.

**Times are written with a dot, and the hour is not padded.** `8.00`, not `08:00`. Both changes are needed for every morning Slot, and a dotted time also reads as a decimal number — `10.20` is twenty past ten, never a tenth of an hour.

**Every line carries two dashes and ends with `ч.`** The first dash separates `N час` from the times, the second separates the two times. Extracted without its layout the page shreds each line into four fragments — `1 час -`, `8.00`, `-`, `8.40 ч.` — and a column of loose dashes reassembles into the wrong pairing easily. Read the page with its layout preserved, and use the eight `ч.` markers to confirm eight rows and no more.

**The watermark extracts as if it were content.** The π logo carries the strings `a ∫x.(dx) 2x` and `f = 2x - 4b + 9a`, set large and pale and overlapping the rows. A reading that goes by eye picks up fragments of it lying between the lines, with digits, dots and dashes in them. Nothing that is not an `N час` line is part of the School day.

**`3.9.2026 г.` in the signature block is a date.** It is when the director approved the schedule, printed in tiny type above the signature line, and it is the only other number on the page. It is not a time, and it is not the provenance the document asks for.

**The filename is not about four hours.** `grafik-y4.4asove` is `уч.часове` transliterated with `4` standing in for `ч`. Read as "4 часа" it suggests this is the short-day variant of a schedule that has variants; it is not, and there are none.

**The slot numbers are a check, not data.** A Slot carries no number — the Timetable names it by its position among the Slots, counting from 1 and ignoring Breaks. Here the printed numbers agree with those positions because the day starts at `1 час` and there is no `0 час`, so emit the entries in order and drop the numbers. If a future revision opens with a zeroth hour, the two stop agreeing and the numbers are what says so.

## Worked example

Given the first four lines of the schedule:

```
1 час -     8.00   -    8.40 ч.
2 час -     8.50   -    9.30 ч.
3 час -     9.40   -    10.20 ч.
4 час -     10.40  -    11.20 ч.
```

Produce:

```json
{
  "schemaVersion": "1",
  "sequence": [
    { "kind": "slot",  "start": "08:00", "end": "08:40" },
    { "kind": "break", "start": "08:40", "end": "08:50" },
    { "kind": "slot",  "start": "08:50", "end": "09:30" },
    { "kind": "break", "start": "09:30", "end": "09:40" },
    { "kind": "slot",  "start": "09:40", "end": "10:20" },
    { "kind": "break", "start": "10:20", "end": "10:40", "label": "голямо междучасие" },
    { "kind": "slot",  "start": "10:40", "end": "11:20" }
  ]
}
```

Three of these seven entries appear in no row of the artifact, and neither does the one label — the Break after Slot 3 is the `голямо междучасие` by being twenty minutes long. The whole document is fifteen entries: eight Slots, the seven Breaks between them, and no Break before the first Slot or after the last.
