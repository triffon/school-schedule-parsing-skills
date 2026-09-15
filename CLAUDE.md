# school-schedule-parsing-skills

The shared **Parsing Skill** library for [school-schedule](https://github.com/triffon/school-schedule), consumed there as a submodule mounted at `skills/`.

Nothing here is executed and there is nothing to build, test or lint. Every file is prose an agent reads at parse time.

## Before editing a Skill

- `docs/skill-format.md` is the contract every file under `skills/` obeys — frontmatter fields, the four body sections, and what deliberately does not belong in a Skill.
- `docs/skill-template.md` is the starting point for a new one.
- The domain vocabulary — Intake, Slot, Break, Lesson, Block, Term, Non-school day, Provenance — is defined in the pipeline's `CONTEXT.md`, and the schema those documents are held to is its `src/intake/intake.schema.json`. Use those terms as defined; do not restate the schema here.

## Scope

A Skill captures how one publisher lays out one artifact. It never carries a school's data, never restates the Intake schema, and never asks an agent to derive anything the pipeline computes — Blocks in particular are formed from consecutive Lessons by the pipeline, so a Skill asks for cells.

`skills/example-school/` is fictional and exists to show the shape. Every other Skill here has been checked against a real artifact and names it: the `ministry-of-education-and-science/` pair against заповед РД09-3345/31.08.2026, the `parva-chastna-matematicheska-gimnazia/` pair against that school's `График на учебните седмици` for 2026/27, `edupage/timetable` against `fpmg.edupage.org/timetable/view.php?num=42&class=*1`. When a publisher changes its layout, correct the Skill in place against the new artifact rather than adding a second one beside it.
