---
name: publisher-slug/artifact-slug
publisher: The body that publishes the artifact, written out for a human
artifact: One sentence naming the thing being parsed and where it appears
document: schoolDay | timetable | nonSchoolDays | term
schemaVersion: "1"
---

# <Publisher>: <artifact>

<!--
Copy this file to skills/<publisher-slug>/<artifact-slug>.md and fill it in
against a real artifact. `name` must equal that path with skills/ and .md
stripped. See docs/skill-format.md for what each section is for.
Delete every comment, including this one, before committing.
-->

## The Source

<!--
Where the artifact is published, what it is usually called, when in the year it
appears. Then what it is *not*: the near-identical artifact from the same
publisher that this Skill does not cover.
-->

## Reading it

<!--
The layout, and where each field comes from. Name the real landmarks — which
column holds the times, which row is the header, how the rows are ordered.
Work from the artifact's structure to the schema's fields.
-->

## Quirks

<!--
The publisher-specific traps: the reason this Skill exists. A blank cell that
means "no lesson" against one that means "same as above". An abbreviation only
this publisher uses. A footnote that overrides the grid. Each of these is a
misreading that would otherwise pass validation.

If a quirk was found by getting it wrong once, say so.
-->

## Worked example

<!--
A real fragment of the artifact, and the JSON it yields. Small enough to read in
one screen, large enough to exercise at least one quirk above. Omit provenance —
the emitted prompt asks for it separately.
-->

Given:

```
<a fragment of the artifact>
```

Produce:

```json
{
  "schemaVersion": "1"
}
```
