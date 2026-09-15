# The Parsing Skill format

Every file under `skills/` obeys this contract. It exists so that the pipeline can resolve a Skill and embed it in a prompt without reading it, and so that an agent handed one knows what it is looking at before it reaches the prose.

A Skill is YAML frontmatter followed by Markdown. The frontmatter is what the pipeline reads; the Markdown is what the agent reads.

## Frontmatter

```yaml
---
name: example-school/timetable
publisher: Example School
artifact: The weekly timetable PDF published on the school's website each August
document: timetable
schemaVersion: "1"
---
```

**`name`** — the Skill's identity, and the string an operator passes to the pipeline. It must equal the file's path under `skills/` with the `.md` dropped, so the name and the location cannot disagree.

**`publisher`** — the body that publishes the artifact, written out for a human. A school for its own Timetable and School day; a ministry, a municipality or a regional directorate for artifacts it mandates nationally.

**`artifact`** — one sentence naming the thing being parsed and where it appears, precisely enough that an operator holding two files from the same publisher can tell which Skill applies to which. Not a file format: "the weekly timetable PDF published each August" identifies an artifact, "a PDF" does not.

**`document`** — which of the four Intake documents this Skill produces. Exactly one of `schoolDay`, `timetable`, `nonSchoolDays`, `term`, spelled as the pipeline spells them. A Skill produces one document and no more, because the four are re-parsed on completely different cadences and a Skill covering two would drag one along whenever the other changed.

**`schemaVersion`** — the version of the Intake contract the Skill was written against, quoted so YAML keeps it a string. A Skill written against an older contract is not silently trusted against a newer one.

One artifact sometimes carries two documents — a ministry order that sets both the Term and the holidays is the usual case. That is two Skills over the same artifact, differing in `document`, not one Skill producing both.

## Body

Four sections, in this order. An agent reads them in order and each answers a question the previous one raised.

### The Source

How to recognise the artifact and where it comes from: the page it is published on, what it is usually called, when in the year it appears, and what it is *not* — the near-identical artifact from the same publisher that this Skill does not cover. An operator has already chosen this Skill by name, so this section is a confirmation, and its job is to make a mismatch obvious before anything is transcribed.

### Reading it

The layout, and where each field of the Intake document comes from. Name the actual landmarks: which column holds the times, which row is the header, which corner the class name sits in, how the rows are ordered. Work from the artifact's structure to the schema's fields, not the other way round, because the agent is looking at the artifact.

### Quirks

The publisher-specific traps — the reason this Skill exists rather than the agent guessing. A blank cell that means "no lesson" against one that means "same as above". A row that repeats the header partway down a long table. An abbreviation only this publisher uses, and what it expands to. A footnote that overrides the grid above it. Times written with a dot rather than a colon. Each of these is a misreading that would otherwise pass validation, because a misread cell is still a well-formed cell.

Write them as observations about the artifact, with the correct handling attached. If a quirk was found by getting it wrong once, say so — that is the most valuable line in the file.

### Worked example

A fragment of the real artifact, and the JSON it yields. Small enough to read in one screen, large enough to exercise at least one quirk. This is the section an agent leans on hardest, and it is the section that makes a Skill worth sharing rather than rewriting.

The JSON fragment shows the document's own fields. Leave `provenance` out of it — the emitted prompt asks for that separately and it would only be noise here.

## What does not belong in a Skill

**The schema.** The prompt embeds `intake.schema.json` verbatim alongside the Skill. Restating field types here creates a second contract that will drift from the first.

**Generic parsing advice.** "Read the table carefully", "watch out for merged cells". The model does not need it and it dilutes the quirks that matter.

**A school's actual data.** A Skill describes a layout. The values belong in a data repository's Intake, which is that school's to keep private.

**Derived structure.** Blocks are computed by the pipeline from consecutive Lessons naming the same subject. A Skill never asks an agent to merge anything — it asks for cells.
