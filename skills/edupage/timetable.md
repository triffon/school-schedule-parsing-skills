---
name: edupage/timetable
publisher: EduPage (aSc Applied Software Consultants) — the platform several hundred schools publish their timetables through. The layout is EduPage's and identical everywhere; the data is each school's.
artifact: A class's public weekly timetable at <school>.edupage.org/timetable/view.php?num=<n>&class=<id> — one URL per class per published timetable
document: timetable
schemaVersion: "1"
---

# EduPage: a class's weekly timetable

## The Source

A URL of the form:

```
https://fpmg.edupage.org/timetable/view.php?num=42&class=*1
```

`num` names one published timetable — a school republishes per срок and per school year, and every past one stays reachable. `class` is an internal identifier, not the class's name: `*1` is whatever the school happens to have entered first, and the page is the only thing that says which Class it is. Both come from the operator, who took the URL out of the school's timetable page; neither is guessable and the Skill must not invent either.

This is keyed by platform rather than by school because EduPage is what shapes it. Every school on `*.edupage.org` serves this same page from the same endpoints with the same field names, so one Skill reads all of them and a school-specific one would be the same file retyped.

It is not the teacher view (`?teacher=...`) or the classroom view (`?classroom=...`) of the same timetable, which carry the same lessons indexed the other way round. It is not `/substitution/`, the daily changes feed — moved and cancelled lessons are dated, and the Intake describes the recurring week. And it is not the school's own bell-times artifact: EduPage knows the period times and this Skill deliberately ignores them, because the School day is a separate document parsed from the school's own published schedule.

## Reading it

**The URL does not serve the timetable.** It serves a 45 KB application shell; the grid is built in the browser. Do not parse the fetched HTML — see the first quirk. Read the shell only to confirm two things, from the `renderRootComponent` call near the end of `<body>`:

```js
a.renderRootComponent(gi1277,"/timetable/ttviewer.js#TTViewer",{"num":"42","user":"Trieda*1","fitheight":true},[]);
```

`num` echoes the URL, and `user` is `Trieda` followed by the class identifier. If either disagrees with the URL the operator gave you, stop and say so.

Then ask the server for the data, twice.

**Which timetables exist**, so you can tell whether `num` is the current one:

```sh
curl -sS "https://fpmg.edupage.org/timetable/server/ttviewer.js?__func=getTTViewerData" \
  -H "Content-Type: application/json" \
  --data '{"__args":[null,2026],"__gsh":"00000000"}'
```

It answers with `r.regular.default_num` and `r.regular.timetables`, each entry carrying `tt_num`, a `text` such as `2026-2027 1srok (15. 09. 2026 - 29. 01. 2027)`, a `datefrom` and a `hidden` flag. The second argument is the school year the timetables are filed under.

**The timetable itself**, which is the whole aSc database behind it:

```sh
curl -sS "https://fpmg.edupage.org/timetable/server/regulartt.js?__func=regularttGetData" \
  -H "Content-Type: application/json" \
  --data '{"__args":[null,"42"],"__gsh":"00000000"}'
```

The response holds `r.dbiAccessorRes.tables`, a list of `{ "id": <table name>, "data_rows": [...] }`. Five tables matter:

- **`classes`** — `{ id, short, name }`, where `short` is `5А` and `name` is `5А клас`. Confirm that the `class` identifier from the URL is the Class the operator named.
- **`subjects`** — `{ id, name, short }`.
- **`lessons`** — what is taught: `{ id, subjectid, classids, groupids, durationperiods }`. A lesson is a teaching commitment, not a position in the week.
- **`cards`** — where it sits: `{ id, lessonid, period, days }`. This is what the grid draws.
- **`groups`** — `{ id, name, classid, entireclass }`, for reading a split.

Every Lesson of the Intake comes from a card whose lesson concerns this Class:

- keep the cards whose `lessons[lessonid].classids` **contains** the class identifier;
- `weekday` from `days`, a five-character bitstring with Monday leftmost: `10000` is Monday, `01000` Tuesday, through `00001` for Friday;
- `slot` from `period`, a decimal string counting from `1`, which is already the Slot's position among the School day's Slots;
- `subject` from `subjects[lessonid → subjectid].name`;
- and one Lesson per period the card covers, not one per card — `durationperiods` says how many.

## Quirks

**Fetching the URL gets you a page with no timetable in it.** The HTML is navigation, styling and a script tag; there is no table, no subject name and no time of day anywhere in it. An agent that reads what it fetched concludes the class has no lessons at all and emits an empty `lessons` array, which is structurally valid and completely wrong. This was found by doing exactly that. The grid exists only after `/timetable/ttviewer.js` has run and called the endpoints above.

**`__gsh` is not a credential.** The shell sets `ASC.gsechash="00000000"` for an anonymous visitor and the endpoints answer on that, unauthenticated, for any timetable the school has published. Send the literal `00000000`; if a school ever stops accepting it, read the real value out of `ASC.gsechash` in the shell and send that instead. A public timetable needing a login is a signal to stop and ask the operator, not to find a way in.

**`durationperiods` is the trap that silently halves the week.** A double period is **one** card, and the grid draws it as one tall cell. Monday's `Български език и литература` for 5А is a single card at `period: "1"` with `durationperiods: 2`, and it is Lessons in Slot 1 *and* Slot 2. Take one Lesson per card and 253 of this school's 648 cards lose their second hour, leaving a week that validates, looks like a light timetable, and is wrong. Emit `durationperiods` consecutive Lessons starting at `period`. Upper classes here run to 3 and 4.

Emit them as separate Lessons even though they name the same subject. Merging is the pipeline's job and it does it from consecutive Lessons; a Skill hands over cells.

**A card with an empty `days` and an empty `period` is not on the grid.** Card `*648` of this school is one: a lesson the school has planned and not yet placed. It has no weekday and no slot, so it is not a Lesson. Skip every card whose `days` or `period` is the empty string rather than defaulting it to Monday or to Slot 1.

**`classids` is a list, and its order means nothing.** A lesson taught to groups drawn from several classes carries all of them. 5А's `Математика ФЧ` on Wednesday names `["*1","*2","*3","*4"]` on one row and `["*3","*1","*2","*4"]` on the next — the same four classes, reordered. Match by membership. Testing `classids[0]` or `classids == [id]` drops this Class's lessons on some rows and not others, which is worse than dropping all of them because the result still looks like a timetable.

**A split class puts several cards on one weekday and slot, and the Intake allows one Lesson there.** Two shapes occur, and they are not the same problem:

- *Same subject in every group.* 5А's `КМИТ` on Tuesday, Slot 4, is two cards — `Първа група` and `Втора група` — both `subjectid: "-57"`. Whichever group the student is in, they are in `КМИТ`. Emit one Lesson. Likewise `Математика ФЧ`, four cards across `Група1`–`Група4`, all one subject.
- *Different subjects per group.* A language split — `Английски език` against `Немски език` — where the artifact genuinely does not say which one this student attends. Ask the operator. Do not pick the first card, the larger group, or the alphabetically earlier subject.

`groups[groupid]` tells the two apart: `entireclass: true` is the undivided class, and `name` is what the split is called.

**Never key subjects off `short`.** `Математика` and `Музика` both have `short: "М"` in this school. Both are in 5А's week, on Wednesday, in adjacent Slots 3 and 4. A reading that goes by the short form turns the music lesson into a maths lesson and produces a week no validator will question. Use `name`.

**Several `name`s are themselves abbreviations, and stay as they are.** `КМИТ`, `ИТ`, `ФВС-Д`, and the `ФЧ`, `ИЧ` and `ПП` suffixes that mark факултативни, избираеми and профилирани часове. There is no legend on this page and no expansion in the data: the `name` field is the school's own wording, it is what appears on the student's own timetable, and it is what gets published verbatim. Expanding it from outside knowledge makes next year's re-parse a diff full of changes nobody made.

**The `num` an operator pastes may be a timetable from years ago.** This school still serves `num=22`, `2020-2021-1srok`, flagged `hidden: true` and reachable by anyone who has the link. Nothing in the timetable data itself dates it. Check the chosen `num` against `default_num` and against the `text` and `datefrom` from the ttviewer listing, and against the Term being published; say so rather than parsing on if they disagree.

**The `periods` table is the bell schedule, and it is not this document.** It carries `starttime` and `endtime` per period and duplicates the School day, which is parsed from the school's own published artifact. Emit none of it. Use it only as a check: the highest `period` any card uses must fit inside the School day's Slots, and if it does not, one of the two documents is from the wrong year.

**`globals[0].settings.m_strDateBellowTimeTable` is the Term.** Here it reads `Валидност: 15/09/2026-29/01/2027`. It is the footer the school prints under the grid, it is not the Timetable, and it belongs to whichever Skill parses the Term.

**Alternating weeks and split terms would not survive this document, and this school has none.** Every card here carries `weeks: "1"` and `terms: "1"`, and every `weeksdefid` and `termsdefid` resolves to a `typ: "all"` definition, so the week really does recur unchanged. Should a school's lesson resolve to a `typ: "one"` week or term definition, it happens in alternate weeks only and the Intake has no way to say so. Report that rather than flattening it into a lesson that happens every week.

## Worked example

Given Tuesday for 5А — `class=*1`, the cards whose `days` is `01000` — with the `lessons` rows they point at folded in and the irrelevant fields dropped:

```json
{ "id": "*123", "lessonid": "*101", "period": "1", "days": "01000" }
  lessons/*101: { "subjectid": "-5",  "classids": ["*1"], "groupids": ["*1"], "durationperiods": 2 }
{ "id": "*41",  "lessonid": "*35",  "period": "3", "days": "01000" }
  lessons/*35:  { "subjectid": "-13", "classids": ["*1"], "groupids": ["*1"], "durationperiods": 1 }
{ "id": "*232", "lessonid": "*188", "period": "4", "days": "01000" }
  lessons/*188: { "subjectid": "-57", "classids": ["*1"], "groupids": ["*7"], "durationperiods": 1 }
{ "id": "*233", "lessonid": "*189", "period": "4", "days": "01000" }
  lessons/*189: { "subjectid": "-57", "classids": ["*1"], "groupids": ["*6"], "durationperiods": 1 }
{ "id": "*119", "lessonid": "*99",  "period": "5", "days": "01000" }
  lessons/*99:  { "subjectid": "-1",  "classids": ["*1"], "groupids": ["*1"], "durationperiods": 2 }
{ "id": "*604", "lessonid": "*502", "period": "7", "days": "01000" }
  lessons/*502: { "subjectid": "-22", "classids": ["*1"], "groupids": ["*1"], "durationperiods": 1 }
{ "id": "*33",  "lessonid": "*28",  "period": "8", "days": "01000" }
  lessons/*28:  { "subjectid": "-18", "classids": ["*1"], "groupids": ["*1"], "durationperiods": 1 }
```

against `subjects` — `-1` Български език и литература, `-5` Математика, `-13` География и икономика, `-18` Човекът и природата, `-22` Музика, `-57` КМИТ — and `groups`, where `*1` is `Цял клас` with `entireclass: true` while `*6` and `*7` are `Първа група` and `Втора група`, produce:

```json
{
  "schemaVersion": "1",
  "lessons": [
    { "weekday": "tuesday", "slot": 1, "subject": "Математика" },
    { "weekday": "tuesday", "slot": 2, "subject": "Математика" },
    { "weekday": "tuesday", "slot": 3, "subject": "География и икономика" },
    { "weekday": "tuesday", "slot": 4, "subject": "КМИТ" },
    { "weekday": "tuesday", "slot": 5, "subject": "Български език и литература" },
    { "weekday": "tuesday", "slot": 6, "subject": "Български език и литература" },
    { "weekday": "tuesday", "slot": 7, "subject": "Музика" },
    { "weekday": "tuesday", "slot": 8, "subject": "Човекът и природата" }
  ]
}
```

Seven cards, eight Lessons. Slots 2 and 6 come from `durationperiods: 2` and appear in no card of their own; Slot 4's two cards are one Lesson because both are `КМИТ`; and `Музика` is Slot 7 rather than `Математика` because the subject came from `name` and not from the `М` they share.
