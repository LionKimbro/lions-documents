```
document-id: project-structure.python-2026-03.agent-guide.compact.v1
title: Lion's `python-2026-03` Project Structure — Compact Agent Guide
purpose: Describe the python-2026-03 project structure for agents -- in a compact way.
tags: project structure guide agent compact
document-type: reference
document-created: 2026-07-14
```

# Lion's `python-2026-03` Project Structure — Compact Agent Guide

This is Lion Kimbro's common Python project archetype. It is a default, not a rigid schema.

A project folder is a complete working habitat: code, tests, docs, provenance, tasks, examples, metadata, and public orientation.

## Typical Layout

```text
project-root/
├── db/
├── docs/
│   ├── raw/
│   ├── architecture/
│   ├── manual/
│   └── manual-html/
├── examples/
├── guitests/
├── src/<package>/
├── tests/
├── .zookeep/
├── LICENSE
├── pyproject.toml
├── README.md
├── run-tests.bat
└── zoo-project.json
```

Not every project has every directory.

## Project Roots

On Robert's computer:

- public projects: `C:/lion/github/`
- private projects: `C:/lion/code/`

Paths vary by machine. Do not hard-code these locations.

## `db/`

Living operational project data.

Common files:

- `db/rules.md`: local instructions for agents and maintainers
- `db/tasks.md`: backlog and project tasks

Also suitable for indexes, coordinates, status, and small machine-readable registries.

Read `db/rules.md` early. Do not assume every task is authorized for immediate execution.

## `docs/raw/`

Append-oriented source stream containing transcripts, guides, specs, addenda, agent guides, and project history.

Filename pattern:

```text
001__descriptive-name.md
002__descriptive-name.json
```

Rules:

- prefer three digits for new files;
- numbers increase monotonically;
- never renumber old files for neatness;
- after a long hiatus, Lion may resume at the next ten;
- `raw` means source material, not disposable material.

The first file is often:

```text
001__conversations.json
```

This stores links to conversations, Sparks, and other project origins.

Preserve provenance and return paths.

## Document Relationships

Lion wants consistent headers and especially:

```text
supersedes
superseded-by
```

When replacing a guide, create a new numbered file and link the two. Do not erase project history.

## Other `docs/` Locations

- `docs/architecture/`: usually ADRs
- `docs/manual/`: often living manual source
- `docs/manual-html/`: often generated output, not source
- `docs/code/`: maintained explanations of the implementation and is typically maintained by coding agents

Check `db/rules.md` before editing generated documentation.

## `examples/`

Mostly used by infrastructure/library projects.

Each file should be a small runnable capability demonstration. Keep examples current with the public API.

## `guitests/`

Used by some Tkinter projects, often with `tkintertester`.

Use for real Tk lifecycle and GUI integration behavior. Put ordinary logic tests in `tests/`.

## `src/`

Use the `src` layout:

```text
src/<package>/
```

Lion often prefers mostly flat, thematic modules, with subpackages only for real divisions such as `ui/`.

Do not impose deep hierarchies or class-heavy redesigns without a concrete need.

## `tests/`

Ordinary automated tests.

Add regression tests for bugs. Do not weaken tests merely to make failures disappear.

## `run-tests.bat`

Encouraged. Lion should not have to remember each project's test command.

Keep it short, readable, project-relative, and make it return the test process exit code.

## `pyproject.toml`

Very important.

Strong defaults:

- use `[project.scripts]` for meaningful commands;
- declare dependencies explicitly;
- declare dependencies on Lion's other packages;
- check package and distribution names before changing them;
- remember some projects are published to PyPI.

A large majority of Lion's projects use `[project.scripts]`. When in doubt, prefer a proper installed command.

## `README.md`

The public front door.

It should explain:

- what the project is;
- why it exists;
- how to install or run it;
- a minimal example;
- links to deeper docs;
- links to related Lion projects and outside work.

## License

Most projects use:

```text
CC0-1.0
```

Do not change the license casually. Keep `LICENSE`, `pyproject.toml`, Zoo metadata, and README claims consistent.

## `zoo-project.json`

Describes the project folder to Lion's Zoo system.

Example fields:

```json
{
  "zookeep-project-guid": "...",
  "name": "project-name",
  "repo-type": "python-2026-03",
  "license": "CC0-1.0",
  "repository": {
    "name": "project-name",
    "visibility": "public"
  },
  "python-package": {
    "name": "package_name"
  }
}
```

Rules:

- preserve the GUID;
- `repo-type` names this archetype;
- repository and package names may differ;
- Zoo metadata is evolving;
- do not migrate it into `db/` without an explicit plan.

## `.zookeep/`

Tool-created operational state or marker directory.

It may be empty while state lives elsewhere. Do not delete it without understanding tool ownership.

## Agent Reading Order

Usually inspect:

1. `zoo-project.json`
2. `db/rules.md`
3. `README.md`
4. `pyproject.toml`
5. relevant `db/tasks.md`
6. current guides in docs/
7. relevant materials in docs/code/
8. docs/architecture/ (ADRs)
9. recent or relevant files in `docs/raw/`
10. source and tests
11. examples and GUI tests

## Default Agent Behavior

- Preserve numbered document chronology.
- Avoid hard-coded machine paths.
- Use project-relative paths and explicit configuration.
- Add `[project.scripts]` commands when appropriate.
- Declare dependencies.
- Keep README and examples synchronized with behavior.
- Preserve Zoo IDs.
- Respect source-versus-generated distinctions.
- Append replacement documents instead of overwriting history.
- Prefer simple, visible, inspectable structures.
- Do not perform broad cross-project migrations without a shared standard and migration plan.
- Read `docs/code/` before rebuilding codebase understanding from scratch.
- Keep `docs/code/` synchronized with substantial implementation changes.
- Treat `docs/code/` as an agent-maintained current projection, not as historical source.

## Improvements Lion Wants

High priority:

- standard document headers;
- standard `supersedes` / `superseded-by`;
- standard `001__conversations.json` or coordinates format;
- more consistent use of `db/rules.md` and `db/tasks.md`;
- better source-of-truth markers;
- declared dependencies;
- consistent test runners.

Longer-term:

- evolve Zoo metadata;
- distinguish conceptual project identity from folder identity;
- move suitable operational metadata into `db/`;
- make task files harvestable by Silk Road agents;
- project visualizations generated from simple files;
- nightly agent work with clear reports and provenance.

The core principle is: understand the whole project habitat, not only the code.
