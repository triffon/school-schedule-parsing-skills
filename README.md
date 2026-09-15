# school-schedule-parsing-skills

The shared library of **Parsing Skills** for [school-schedule](https://github.com/triffon/school-schedule).

A Parsing Skill is instructions for turning one publisher's artifact into part of an Intake, written for an agent rather than for code. The pipeline deliberately parses nothing itself: it emits a self-contained prompt embedding the Intake JSON Schema and the Skill for the artifact at hand, an agent reads the artifact against that prompt, and the resulting JSON is committed to a school's data repository. The knowledge worth capturing here is "how this publisher lays out its timetable", not "how to read a PDF" — so Skills are keyed by publisher and artifact, never by file format.

Nothing in this repository is executed. Every file is prose an agent reads.

## Layout

```
skills/<publisher>/<artifact>.md
```

The Skill's **name** is that path without `skills/` and without `.md`, so `skills/example-school/timetable.md` is the Skill `example-school/timetable`. That name is what an operator passes to the pipeline and what a resolution failure reports.

The publisher is a directory because the four Intake documents do not come from one place. A school publishes its own Timetable and its own School day, and both are idiosyncratic to that school. The Term and the Non-school days are usually not the school's to decide at all: they are set nationally and published by a ministry, in one artifact that every school in the country reads. The two example publishers here are that split — `example-school` stands in for a single school's own artifacts, `ministry-of-education-and-science` for the state-mandated ones.

That asymmetry is the reason this library is shared rather than vendored per school. A ministry Skill written once serves every operator in the country; a school Skill serves one school and is contributed mainly so the next school sees what a good one looks like.

## Resolution

The pipeline looks for a Skill in the school's data repository first, then in this library:

1. `<data-repo>/skills/<name>.md`
2. `<pipeline>/skills/skills/<name>.md` — this repository, as a submodule

A data repository's own Skill wins, so an operator whose school changed its layout mid-year can fix it immediately and upstream it here afterwards rather than the other way round. A name found in neither place is an error naming both places searched.

## Adding a Skill

Copy [docs/skill-template.md](docs/skill-template.md) to `skills/<publisher>/<artifact>.md`, fill it in against a real artifact, and check the result against [docs/skill-format.md](docs/skill-format.md) — that document is the contract, and it explains what each section is for and why.

A Skill earns its place by being specific. Generic advice about reading tables belongs in the model, not here; what belongs here is the column the school leaves blank, the row it repeats, the abbreviation only its teachers use.

## Consuming this repository

As a submodule of the pipeline, mounted at `skills/`:

```sh
git submodule update --init
```

It can also be cloned on its own and read directly — it is only Markdown.

## Licence

GPL-3.0-or-later, matching the pipeline. See [LICENSE](LICENSE).
