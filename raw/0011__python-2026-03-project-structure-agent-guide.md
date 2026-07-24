```
document-id: project-structure.python-2026-03.agent-guide.full.v1
title: Lion's `python-2026-03` Project Structure — Full Explanation
purpose: Describe the python-2026-03 project structure for agents -- in excruciating detail.
tags: project structure guide agent full
document-type: reference
document-created: 2026-07-14
```

# Lion's `python-2026-03` Project Structure

## Big-Picture Orientation for AI Coding Agents

This document describes the common filesystem structure, meanings, expectations, and evolving conventions used across Lion Kimbro's Python projects.

The structure is identified in Zoo metadata as:

```json
"repo-type": "python-2026-03"
```

This is an archetype, not an inflexible schema. Most of Lion's Python projects follow it closely, but some projects have special-purpose layouts. Examples of exceptions include `lions-documents` and `lions-db`.

(Post-scripted notes: 2026-07-15:)
- It's also expected that in the future, there will be other forms of Python-based project layouts.  And in the past, there have certainly been many different formats as well.  This is the format that has come to dominate this period however.  Hence the name: "python-2026-03".
- Also, it is expected that this very format will develop and expand.

An AI coding agent should use this guide to understand:

- what each common directory means;
- which files are public-facing, operational, historical, generated, or authoritative;
- what defaults Lion expects when adding or reorganizing code;
- how project memory and provenance are preserved;
- what parts of the structure are still evolving;
- which improvements should be made carefully and consistently across projects.

The main design principle is that a project directory is not merely a source-code container. It is the project's **working territory**: code, documentation, provenance, operational data, examples, tests, generated manuals, architectural decisions, and machine-readable project identity all live together.

The filesystem should remain inspectable by a human and legible to an AI agent. Important facts should have obvious homes. The agent should prefer visible conventions and simple files over hidden infrastructure.

---

# 1. Typical Project Layout

A mature project may look approximately like this:

```text
project-root/
├── db/
│   ├── rules.md
│   └── tasks.md
├── docs/
│   ├── raw/
│   ├── architecture/
│   ├── code/
│   ├── manual/
│   └── manual-html/
├── examples/
├── guitests/
├── src/
│   └── package_name/
├── tests/
├── .git/
├── .gitignore
├── .zookeep/
├── LICENSE
├── pyproject.toml
├── README.md
├── run-tests.bat
└── zoo-project.json
```

Not every project has every directory.

Common omissions:

- ordinary application projects may have no `examples/`;
- non-Tkinter projects usually have no `guitests/`;
- smaller or older projects may not yet have `db/`;
- many projects use only `docs/raw/` and do not have the more elaborate documentation tree;
- private projects may not contain GitHub-facing material in exactly the same form;
- some projects predate the archetype or have domain-specific layouts.

The absence of a directory should not automatically be treated as an error. Add structure when it has a clear purpose.

---

# 2. Project Roots and Machine-Local Locations

## 2.1 Public and private projects

On Robert's computer, Lion commonly uses:

```text
C:/lion/github/
```

for public Git repositories, and:

```text
C:/lion/code/
```

for private, local-only projects.

For example:

```text
C:/lion/github/tkmachina/
C:/lion/code/stickerdb/
```

Public is Lion's default. Most projects are intended to be visible, reusable, inspectable, and openly shared unless there is a concrete reason for privacy.

## 2.2 Paths are machine-local

These absolute paths are not universal.

On another computer, including Vector, repository roots may differ. An agent must not bake `C:/lion/github/` or `C:/lion/code/` into project logic unless a machine-local configuration explicitly calls for it.

Use one of the following when a path must vary:

- project-relative paths;
- environment variables;
- configuration files;
- Machine Root or another explicit machine-local registry;
- command-line arguments where appropriate.

## 2.3 Improvement direction

The public/private distinction is meaningful, but absolute machine paths should remain external to project semantics.

Desired improvements:

- document project roots per machine;
- make discovery of shared roots machine-configurable;
- avoid hard-coded workstation paths;
- distinguish project identity from project-folder location;
- clarify how one conceptual project may have multiple folders, repositories, mirrors, or machine-local instances.

---

# 3. `db/`: Living Operational Project Data

## 3.1 Purpose

`db/` is a newer and increasingly important project directory.

It holds small data artifacts that are:

- actively maintained;
- semi-interactive;
- operational rather than narrative;
- important to the project;
- not naturally source code;
- not necessarily appropriate as ordinary documentation.

The name `db` does not imply a relational database. It means something closer to:

> the project's small, living data space.

Typical formats include:

- JSON;
- Markdown;
- LSF;
- other machine-readable or human-editable structured files.

Example:

```text
db/
├── rules.md
└── tasks.md
```

## 3.2 `db/rules.md`

`rules.md` may contain project-local operating rules for coding agents and maintainers.

Example rule:

> Update `docs/manual/` as the living manual source. Do not treat `docs/manual-html/` as the source of truth.

These rules are not necessarily architectural truths about the software itself. They may instead govern how the project should be edited, generated, maintained, or interpreted.

An agent should read `db/rules.md` early.

## 3.3 `db/tasks.md`

`tasks.md` is a project-local task list or backlog.

It may contain:

- unfinished work;
- implementation requests;
- cleanup notes;
- documentation tasks;
- deferred questions;
- tasks intended for future agents;
- items that may eventually be gathered by Silk Road agents.

An agent should not assume that every task is authorized for immediate execution. It should distinguish between:

- current user instructions;
- active project priorities;
- historical backlog;
- speculative ideas;
- completed tasks not yet marked complete.

## 3.4 What belongs in `db/`

Good candidates include:

- task collections;
- agent operating rules;
- machine-maintained indexes;
- project coordinates;
- status records;
- small registries;
- generated metadata that is still intended for inspection;
- documents whose contents are expected to change frequently through use.

Poor candidates include:

- normal Python source;
- public introductory documentation;
- generated build output;
- long historical transcripts;
- authoritative prose manuals;
- large binary assets.

## 3.5 Improvement direction

Lion intends to lean more heavily on `db/`.

Likely future improvements include:

- standardizing common files and their meanings;
- moving some project metadata into `db/`;
- moving project coordinates or conversation indexes into `db/`;
- defining which files are authoritative and which are projections;
- making task files harvestable by Silk Road agents;
- introducing stable, machine-readable formats where beneficial;
- preserving human readability;
- distinguishing operational data from historical source material.

An agent should not perform broad migrations into `db/` without a project-wide plan. The direction is promising, but not fully standardized.

---

# 4. `docs/`: Project Memory, Meaning, and Generated Knowledge

## 4.1 Importance

`docs/` is critical.

For Lion's projects, documentation is not an afterthought. It carries:

- the original thinking behind the project;
- conversations that led to design decisions;
- specifications;
- human guides;
- AI coding-agent guides;
- architectural decisions;
- generated manuals;
- code-oriented explanations;
- project provenance;
- revision history;
- supersession relationships.

A coding agent should inspect `docs/` before making substantial architectural changes.

## 4.2 Typical forms

Many projects contain only:

```text
docs/raw/
```

More elaborate projects may contain:

```text
docs/
├── raw/
├── architecture/
├── code/
├── manual/
├── manual-html/
└── model.md
```

The meaning of a special-purpose directory should be determined from its contents and any project-local rules.

---

# 5. `docs/raw/`: Numbered Source Stream

## 5.1 Core idea

`docs/raw/` contains the input stream from which more organized project understanding may later be derived.

These files may include:

- long transcripts of Lion speaking;
- ChatGPT or Wing-Cat write-ups;
- human guides;
- addenda;
- specs;
- softspecs;
- AI coding-agent guides;
- conversation indexes;
- successive revisions of the same document;
- important source documents that have not been fully organized.

The folder is intentionally only lightly organized.

The governing intuition is:

> Just a number for you.

Rather than prematurely designing a deep taxonomy, documents are appended in sequence.

## 5.2 Filename convention

Files are prefixed with monotonically increasing numbers.

Example:

```text
001__conversations.json
002__original-write-up-by-wing-cat.md
003__chronomark-human-guide.md
004__chronomark-human-guide-addendum.md
005__chronomark-human-guide.md
006__chronomark-human-guide.md
007__chronomark-softspec.json
008__chronomark-ai-coding-agent-guide.md
009__chronomark-human-guide-addendum.md
```

The current preference is a three-digit prefix:

```text
001
002
003
...
```

Older projects may use two digits or four digits. Do not renumber old files merely for cosmetic consistency.

The separator is typically:

```text
__
```

so a filename has this general shape:

```text
NNN__descriptive-name.ext
```

## 5.3 Monotonic numbering

Numbers should increase monotonically.

Do not insert a new file into an earlier number simply because it conceptually belongs there. The sequence records the evolution of the project.

If a project has been dormant for a while, Lion may resume at the next multiple of ten.

Example:

```text
009__...
020__resumed-project-orientation.md
```

The gap visually marks a new period of activity.

## 5.4 Raw does not mean disposable

`raw` means source material, not junk.

These files may be the best available record of:

- why a design exists;
- what alternatives were rejected;
- what Lion actually intended;
- how terminology evolved;
- which conversation introduced a concept;
- which document replaced an earlier one.

Agents must not casually delete, rewrite, squash, or relocate raw documents.

## 5.5 Future projection

Lion's long-term intention is that these documents may be projected into a visual project interface.

The raw files remain simple and append-oriented, while software may later provide:

- timelines;
- grouping;
- document relationships;
- supersession chains;
- project maps;
- task extraction;
- conversation return paths;
- document-type filters;
- generated summaries;
- views over project evolution.

Because of this, raw documents should remain stable, inspectable, and addressable.

## 5.6 Improvement direction

Desired improvements include:

- standard document headers;
- explicit document IDs;
- clear creation or recording dates;
- author or source identification;
- document type;
- project association;
- status;
- tags;
- source conversation;
- `supersedes`;
- `superseded-by`;
- links to derived or generated documents;
- consistent naming conventions;
- machine-readable indexes without destroying the append-only feel.

The most important relationship fields are:

```text
supersedes
superseded-by
```

These matter because several documents may have the same human-readable title while representing successive states of the project.

An agent should preserve older versions and create explicit relationships rather than silently replacing historical documents.

---

# 6. `001__conversations.json`: Provenance and Return Paths

## 6.1 Common role

The first file in `docs/raw/` is often:

```text
001__conversations.json
```

It points back to conversations, sparks, or other sources from which the project emerged.

Example:

```json
{
  "items": [
    {
      "type": "spark",
      "coordinates": {
        "uuid": "97438360-12ab-4621-bc36-d50a2e7a5170",
        "title": "ChronoMark"
      }
    },
    {
      "type": "conversation",
      "coordinates": {
        "hook": "initial conversation",
        "url": "https://chatgpt.com/c/..."
      }
    }
  ]
}
```

## 6.2 Why this matters

Lion's work is often distributed across many conversations and many moments.

The conversation index preserves:

- project provenance;
- access to original reasoning;
- continuity after interruption;
- links between related inquiries;
- a route back to discussions that are not fully represented in current source files.

The index is part of the project's memory.

## 6.3 Interpretation

The entries may refer to:

- ChatGPT conversations;
- Sparks;
- source documents;
- external resources;
- related projects;
- future coordinate types.

The `hook` should explain why a conversation matters, not merely label it "conversation 2."

## 6.4 Improvement direction

This format is not yet standardized.

Desired improvements include:

- a shared schema or SoftSpec;
- consistent item types;
- stable field names;
- standard coordinate shapes;
- document identity;
- project identity;
- optional dates;
- optional relationship labels;
- validation or normalization tools;
- migration of the active index into `db/`, while preserving a raw historical record if appropriate;
- shared conformance across projects.

A future Silk Road task may standardize these coordinate lists across Lion's projects.

Until a standard is adopted, agents should preserve existing data and extend it conservatively.

---

# 7. Other Documentation Directories

## 7.1 `docs/architecture/`

This usually stores architecture decision records, or ADRs.

ADRs are generally authored decisions rather than generated manuals.

They should explain:

- the context;
- the decision;
- alternatives considered;
- consequences;
- status;
- relationships to earlier decisions.

An agent should not overwrite an accepted ADR to reverse a decision. It should create a new ADR that supersedes or amends the old one.

## 7.2 `docs/manual/`

This may be the living source for a user or developer manual.

Project-local rules determine authority. For example, a project may state:

```text
docs/manual/ is the source of truth.
```

An agent must check `db/rules.md`, README instructions, or generation scripts before editing manual outputs.

## 7.3 `docs/manual-html/`

This may contain generated HTML derived from the living manual.

It is often not the source of truth.

Do not hand-edit generated HTML unless the project explicitly says to.

## 7.4 `docs/code/`

`docs/code/` contains maintained explanations of the codebase and its internal operation.

It is typically maintained by AI coding agents as they inspect, modify, and better understand the project. Its purpose is to preserve a current, navigable model of how the implementation works so that Lion and future agents do not have to reconstruct the same understanding repeatedly.

Suitable contents include:

* codebase maps;
* module and package overviews;
* runtime and control-flow explanations;
* descriptions of important data structures;
* subsystem relationships;
* lifecycle and event-flow documentation;
* implementation invariants;
* explanations of difficult or non-obvious code;
* maintenance and extension guidance;
* indexes linking code concepts to relevant source files, tests, ADRs, and raw documents.

Documents in `docs/code/` are usually maintained projections of the current implementation rather than historical source material.

They should be updated when significant code changes make them inaccurate.

An agent should normally consult `docs/code/` before reconstructing the architecture directly from source. When the existing documentation is stale, the agent should correct it as part of substantial implementation work.

`docs/code/` is not intended for:

* raw conversations or project history, which belong in `docs/raw/`;
* binding architectural decisions, which belong in `docs/architecture/`;
* public introductory material, which belongs in `README.md`;
* user-facing or developer-facing manuals, which may belong in `docs/manual/`;
* operational instructions for agents, which belong in `db/rules.md`.

Although agents typically maintain this directory, its contents remain ordinary project documentation: human-readable, inspectable, editable by Lion, and subject to project-local rules.


## 7.5 Standalone files in `docs/`

A project may also have files such as:

```text
docs/model.md
```

These may be older, special-purpose, or highly important.

Do not infer authority from location alone. Read the document header, project rules, and surrounding references.

## 7.6 Generated versus authored documentation

Some documentation is produced by Codex or Claude Code.

Generated does not mean unimportant. It may be the primary maintained manual.

However, an agent should know whether a document is:

- raw source or historical project memory;
- an authored architectural decision;
- an agent-maintained explanation of the current codebase;
- a generated projection;
- a current guide;
- a historical guide;
- public-facing documentation;
- internal operational instruction.

Improvement direction:

- mark generation provenance;
- state source-of-truth relationships;
- identify generated outputs;
- add reproducible generation commands;
- prevent agents from editing projections instead of sources.

A typical mapping is:

- `docs/raw/` — source stream, provenance, and history;
- `docs/architecture/` — architectural decisions;
- `docs/code/` — agent-maintained understanding of the current implementation;
- `docs/manual/` — maintained human-facing manual source;
- `docs/manual-html/` — generated manual projection;
- `db/rules.md` — operational instructions for agents and maintainers.

---

# 8. `examples/`: Executable Capability Demonstrations

## 8.1 When it appears

Most projects do not need `examples/`.

Infrastructure projects, libraries, frameworks, and reusable components often do.

`tkmachina` has a flat examples directory where each Python file demonstrates a capability.

Example:

```text
examples/
├── basic_window.py
├── button_example.py
├── entry_example.py
└── multiple_windows.py
```

## 8.2 Purpose

Examples serve several roles:

- teaching users how to use the package;
- demonstrating intended public API;
- making capabilities visible during development;
- revealing missing features;
- providing manual smoke tests;
- offering small starting points for downstream projects.

Examples are part of the product surface.

## 8.3 Agent expectations

Examples should be:

- small;
- runnable;
- focused;
- named clearly;
- independent where practical;
- based on supported APIs;
- updated when APIs change.

Do not turn examples into a second application framework.

## 8.4 Improvement direction

Possible improvements include:

- a README inside `examples/`;
- a stable order from simplest to most advanced;
- automated smoke execution where practical;
- mapping examples to documented features;
- ensuring every important public capability has at least one clear example.

---

# 9. `guitests/`: Tkinter Integration and Visual Tests

## 9.1 Purpose

Tkinter projects may contain:

```text
guitests/
```

These tests often use `tkintertester`.

They are distinct from ordinary unit tests because they run against a real Tk event loop and may exercise behavior that cannot be tested meaningfully without GUI lifecycle participation.

## 9.2 When to use

Use `guitests/` for:

- window lifecycle;
- event flow;
- widget construction;
- focus behavior;
- inter-window behavior;
- visible state transitions;
- regression scenarios involving Tk;
- tests that need a real mainloop.

Ordinary logic should still be tested in `tests/` when possible.

## 9.3 Agent expectations

An agent working on a Tkinter project should check whether `guitests/` already exists and whether `tkintertester` is an expected dependency.

Do not introduce unrelated GUI test frameworks without a concrete reason.

## 9.4 Improvement direction

Possible improvements include:

- a standard command for running GUI tests;
- separating deterministic harness tests from human visual inspection;
- documenting headful-environment requirements;
- integrating GUI tests into `run-tests.bat`;
- maintaining minimal example applications for test fixtures.

---

# 10. `src/`: Python Packages and Application Code

## 10.1 Source layout

Projects generally use the `src` layout:

```text
src/
└── package_name/
    ├── __init__.py
    ├── main.py
    ├── domain.py
    └── ...
```

The package directory typically matches the Python package name declared in project metadata.

## 10.2 Style of module organization

Lion's packages often use direct, thematic modules rather than deep class-heavy hierarchies.

Examples:

```text
src/stickerdb/
├── app_state.py
├── domain.py
├── event_log.py
├── execution_satellite.py
├── filetalk_io.py
├── image_clipboard.py
├── input_replay_satellite.py
├── m1_store.py
├── main.py
├── repository.py
├── slicer.py
├── workers.py
└── ui/
```

The package may be mostly flat, with a subpackage such as `ui/` when there is a real thematic boundary.

An agent should prefer modules that correspond to understandable parts of the system.

## 10.3 Avoid unnecessary structural churn

Do not reorganize the entire package merely because another layout appears more fashionable.

Before splitting or moving modules, consider:

- whether the change improves navigation;
- whether names reflect actual responsibilities;
- whether imports remain simple;
- whether public APIs are affected;
- whether documentation paths must be updated;
- whether downstream projects import these modules directly;
- whether the change creates more conceptual overhead than it removes.

## 10.4 Non-code data inside packages

A package may contain small data files, such as:

```text
sample_data.m1
```

Do not move them automatically. Determine whether they are:

- runtime package data;
- test fixtures;
- examples;
- migration inputs;
- accidental residue.

If they are package resources, ensure packaging configuration includes them.

## 10.5 Improvement direction

Desired improvements are project-specific, but agents should generally support:

- clear module responsibilities;
- explicit entrypoints;
- small public APIs;
- reduced accidental coupling;
- visible dependencies;
- retaining Lion's procedural style;
- avoiding needless class scaffolding;
- maintaining inspectability.

---

# 11. `tests/`: Ordinary Automated Tests

## 11.1 Purpose

`tests/` contains ordinary automated tests.

These may include:

- unit tests;
- integration tests;
- repository tests;
- serialization tests;
- command-line tests;
- regression tests;
- non-GUI application behavior.

## 11.2 Agent expectations

When changing behavior:

- inspect existing tests first;
- update tests when intended behavior changes;
- add regression tests for bugs;
- do not rewrite tests merely to make failures disappear;
- preserve the distinction between expected behavior and current accidental behavior.

## 11.3 Improvement direction

Lion values a simple way to run tests.

Projects should ideally provide:

```text
run-tests.bat
```

or another obvious wrapper.

The test command should not exist only in someone's memory.

---

# 12. `run-tests.bat`: Remembering How to Test

## 12.1 Purpose

`run-tests.bat` is encouraged, especially on Lion's Windows environments.

It provides an obvious, repeatable way to run the project's tests.

The practical motivation is simple:

> Lion should not have to remember the exact test command for every project.

## 12.2 Agent expectations

A good test runner script should:

- run from the project root;
- activate or locate the intended Python environment if the project has a clear convention;
- invoke the correct test command;
- include GUI tests if appropriate, or provide a clearly named separate command;
- return the test process exit code;
- avoid machine-specific paths where possible;
- be short and readable.

## 12.3 Improvement direction

Potential improvements include:

- consistent names across projects;
- companion scripts for linting, formatting, examples, or packaging only when useful;
- a cross-platform equivalent if needed;
- clear output indicating what test suites are being run.

---

# 13. `pyproject.toml`: Packaging, Dependencies, and Commands

## 13.1 Central role

`pyproject.toml` is a major project control file.

Agents should inspect it before:

- adding dependencies;
- changing package names;
- adding commands;
- modifying build behavior;
- changing supported Python versions;
- publishing packages.

## 13.2 `[project.scripts]`

Approximately 90% of Lion's projects use `[project.scripts]`.

This is a strong default.

When a project has a meaningful command-line entrypoint, the agent should generally define it through:

```toml
[project.scripts]
command-name = "package.module:function"
```

If in doubt, favor a proper script entrypoint rather than requiring users to remember:

```text
python -m ...
```

or a path to a source file.

Command names should be stable, memorable, and aligned with the project.

## 13.3 Dependencies

Use the `dependencies` field actively and honestly.

This is especially important for dependencies on Lion's other projects.

Do not rely on an undeclared package merely because it happens to be installed on the current machine.

Example:

```toml
[project]
dependencies = [
    "lionscliapp",
    "tkintertester"
]
```

When dependencies are private, local, editable, or not on PyPI, the surrounding installation workflow may need additional documentation. Still declare the conceptual dependency where the packaging model allows it.

## 13.4 PyPI

Some projects are published to PyPI.

Before making a breaking packaging change, check:

- package name;
- distribution name;
- public imports;
- script names;
- versioning;
- package data;
- downstream dependencies.

## 13.5 Improvement direction

Possible improvements include:

- consistent metadata across projects;
- explicit minimum Python version;
- well-maintained dependencies;
- optional dependency groups where genuinely useful;
- standardized test dependencies;
- less reliance on ambient machine state;
- tooling that checks Zoo metadata against `pyproject.toml`.

---

# 14. `README.md`: The Public Front Door

## 14.1 Audience

`README.md` is displayed on the GitHub repository page and is often the first or only document most people read.

Its main jobs are:

1. explain what the project is;
2. explain why it exists;
3. show how to install or run it;
4. show a minimal example;
5. link to deeper documentation;
6. connect the project to related work.

## 14.2 Related work

Lion wants READMEs to include links to:

- related Lion projects;
- upstream or inspiring projects;
- companion tools;
- documentation;
- examples;
- conceptual background where relevant.

A README should situate the project, not present it as if it arose in isolation.

## 14.3 Agent expectations

Keep the README accurate when public behavior changes.

Do not overload it with every internal detail. It is an introduction and map, not the entire project archive.

## 14.4 Improvement direction

Possible improvements include:

- standard sections while preserving project personality;
- installation commands that actually work;
- command examples tied to `[project.scripts]`;
- links to current manuals rather than superseded documents;
- badges only when they add real value;
- clearer related-project maps.

---

# 15. `LICENSE`: Default to CC0-1.0

Most of Lion's projects use:

```text
CC0-1.0
```

This is the default expectation.

An agent should not change the license casually.

When creating a new public project within this ecosystem, CC0-1.0 is the likely default unless Lion says otherwise.

Ensure that:

- `LICENSE` exists;
- `pyproject.toml` metadata agrees;
- `zoo-project.json` agrees;
- README claims agree;
- copied third-party code retains required notices and is legally compatible.

---

# 16. `.gitignore`, `.git/`, and Repository Hygiene

`.git/` is the repository database.

`.gitignore` excludes generated, local, secret, temporary, cache, or machine-specific files.

Agents should avoid committing:

- `__pycache__/`;
- virtual environments;
- temporary run directories;
- secrets;
- local machine configuration;
- generated artifacts that are not intended to be versioned;
- large binary outputs unless the project deliberately tracks them.

However, do not ignore important project memory merely because it changes often. `db/`, `docs/raw/`, and project metadata are often intended to be versioned.

---

# 17. `zoo-project.json`: Project-Folder Identity and Description

## 17.1 Purpose

`zoo-project.json` describes the project folder to Lion's broader project system, Zoo.

Example:

```json
{
  "zookeep-project-guid": "d30c44ed-df27-408f-acce-590995054157",
  "name": "tkmachina",
  "repo-type": "python-2026-03",
  "license": "CC0-1.0",
  "repository": {
    "name": "tkmachina",
    "visibility": "public"
  },
  "python-package": {
    "name": "tkmachina"
  }
}
```

## 17.2 `zookeep-project-guid`

This GUID identifies the project folder in the Zoo system.

The current name may be slightly misleading. It may identify a major project folder or filesystem realization rather than the full abstract identity of the conceptual project.

Do not regenerate it casually.

## 17.3 `name`

The short project name.

It should generally agree with the repository and package naming unless there is a deliberate distinction.

## 17.4 `repo-type`

For projects following this guide:

```json
"repo-type": "python-2026-03"
```

This says that the folder follows this project archetype.

It is a versioned convention name, not a Python version.

## 17.5 `license`

Usually:

```json
"license": "CC0-1.0"
```

Keep it consistent with the actual license file and packaging metadata.

## 17.6 `repository`

Describes repository-level facts such as:

- repository name;
- visibility.

A private project under `C:/lion/code/` may have no public repository or may describe visibility differently.

## 17.7 `python-package`

Names the Python package associated with the project.

This may differ from the repository name in some projects, so do not assume equality without checking.

## 17.8 Improvement direction

Zoo metadata is expected to evolve.

Possible future changes include:

- moving it into `db/`;
- distinguishing conceptual project ID from folder ID;
- representing multiple repositories or folders;
- representing machine-local instances;
- including documentation coordinates;
- including PyPI distribution identity;
- including script entrypoints;
- including status and lifecycle;
- linking related projects;
- linking Sparks and conversations;
- formalizing schema versions;
- clarifying whether the metadata describes a repository, package, project, folder, or all of these.

Until the format is revised, preserve current fields and extend conservatively.

---

# 18. `.zookeep/`: Tool State and Operational Droppings

## 18.1 Purpose

`.zookeep/` is created by Zoo or LionsCLIApp-related tools.

It is an example of the small operational artifacts Lion calls tool "droppings": files or directories left where a tool has operated.

In some projects the directory may be empty because the real state is stored externally.

## 18.2 Agent expectations

Do not delete `.zookeep/` merely because it is empty.

Before modifying it, determine:

- which tool owns it;
- whether the directory is a marker;
- whether state is stored elsewhere;
- whether it is intended to be committed;
- whether scripts rely on its presence.

## 18.3 Improvement direction

Lion may later revise how LionsCLIApp stores state.

This is not currently a high-priority migration.

Potential improvements include:

- clearer ownership metadata;
- explicit local-versus-external state rules;
- less surprising empty marker directories;
- standard cleanup behavior;
- documented relationship between `.zookeep/` and `zoo-project.json`.

---

# 19. Public-by-Default Project Culture

Lion's default tendency is openness.

Public projects live in the GitHub area and are commonly released under CC0-1.0.

This affects how agents should think:

- prefer reusable design;
- maintain public-facing READMEs;
- avoid embedding private machine details;
- document dependencies;
- preserve project history;
- make examples understandable;
- make commands installable;
- keep related work visible.

Private projects may still use the same archetype, but may contain operational or business-specific material that should not be published.

Before changing repository visibility or moving code between public and private roots, obtain explicit direction.

---

# 20. Silk Road and Future Agent Operations

## 20.1 Vision

Lion's Silk Road vision includes distributed agents—sometimes imagined as digital sherpas—that move through project spaces, collect tasks and directives, and carry work into scheduled or nightly execution.

The desired experience is that small tasks scattered across projects can be gathered, prioritized, and executed by AI agents, producing a morning report such as:

> Here are the things we completed.

## 20.2 Implications for project structure

For this to work, projects need:

- discoverable task files;
- stable project identity;
- standard coordinates;
- clear agent rules;
- machine-readable status;
- links to authoritative documentation;
- safe execution boundaries;
- explicit completion records;
- provenance for agent-created changes.

## 20.3 Agent behavior today

The future system is not yet fully standardized.

Current agents should:

- preserve task wording;
- avoid treating every note as authorization;
- record what they changed;
- update task status when instructed;
- respect project-local rules;
- keep generated reports inspectable;
- avoid silently normalizing all projects;
- create proposals for cross-project migrations before applying them widely.

## 20.4 Candidate future task

One explicit desired task is:

> Standardize the format of the conversations/coordinates list in Lion's projects, then conform projects to the shared standard.

This should eventually be approached as a deliberate cross-project migration with:

- a format specification;
- examples;
- compatibility rules;
- migration tooling;
- backups;
- project-by-project reports.

---

# 21. Source of Truth and Projection Rules

A recurring distinction in Lion's project structure is between:

- source;
- generated projection;
- historical source;
- operational state;
- public summary.

Agents must identify these roles before editing.

Examples:

| Artifact | Likely role |
|---|---|
| `docs/raw/*.md` | historical or source input |
| `docs/manual/` | living manual source |
| `docs/manual-html/` | generated projection |
| `docs/code/` | generated projection, explanation of current implementation |
| `README.md` | public summary and entry point |
| `db/tasks.md` | operational task state |
| `docs/architecture/` | authored decisions |
| `zoo-project.json` | project-folder metadata |
| `pyproject.toml` | packaging and command source |
| `examples/*.py` | executable public demonstrations |

These roles are defaults, not universal truths. Project-local rules win.

When uncertain:

1. inspect `db/rules.md`;
2. inspect nearby README files;
3. inspect generation scripts;
4. inspect document headers;
5. inspect references from other files;
6. preserve existing files until authority is clear.

---

# 22. Document Headers and Supersession

## 22.1 Need

Lion wants more consistent document headers across project documents.

The exact format is not yet finalized, but useful fields may include:

```yaml
document-id:
title:
project:
document-type:
status:
created:
updated:
author:
source:
source-conversation:
tags:
supersedes:
superseded-by:
derived-from:
generated-from:
```

JSON documents may use Lion's standard top-level `document` header style.

Markdown documents may eventually use a consistent front matter or heading block.

## 22.2 Supersession discipline

`supersedes` and `superseded-by` are especially important.

When writing a new version of a guide:

- do not erase the old guide;
- give the new document a new numbered filename;
- state which document it supersedes;
- update the old document's `superseded-by` field when practical;
- update indexes or current-guide pointers;
- preserve historical chronology.

## 22.3 Improvement direction

A future standard should specify:

- canonical field names;
- whether links use paths, IDs, or both;
- behavior when one document supersedes several;
- behavior for partial supersession;
- generated-document relationships;
- how current authoritative documents are identified;
- whether headers are validated;
- how old documents lacking headers are migrated.

---

# 23. Recommended Agent Reading Order

When entering an unfamiliar project, an AI coding agent should usually inspect files in this order:

1. `zoo-project.json`
2. `db/rules.md`, if present
3. `README.md`
4. `pyproject.toml`
5. `db/tasks.md`, if relevant to the assigned work
6. current guides in `docs/`
7. relevant material in `docs/code`
8. `docs/architecture/`
9. recent or relevant files in `docs/raw/`
10. `src/`
11. `tests/`
12. `examples/`
13. `guitests/`

This is not a mandate to read every file. It is an orientation path.

For a narrow bug fix, the agent may need only a subset. For architectural work, the documentation and ADR layers are essential.

---

# 24. Recommended Agent Defaults

Unless the project says otherwise, prefer the following:

- Use the existing `src` layout.
- Put importable code under `src/<package>/`.
- Add a `[project.scripts]` entry for meaningful CLI commands.
- Declare dependencies in `pyproject.toml`.
- Treat CC0-1.0 as the likely license for new public projects.
- Preserve numbered `docs/raw/` chronology.
- Use three-digit numbering for new raw documents.
- Append rather than renumber.
- Resume at the next ten after a major hiatus when that convention is already in use.
- Read project-local rules before editing generated documentation.
- Treat `README.md` as the public front door.
- Add or update examples for public APIs.
- Use `guitests/` and `tkintertester` for real Tk lifecycle tests when appropriate.
- Provide an easy test runner such as `run-tests.bat`.
- Preserve Zoo GUIDs.
- Avoid hard-coded machine paths.
- Prefer simple, inspectable files.
- Do not introduce deep hierarchy without a clear reason.
- Do not silently migrate evolving formats.
- Record supersession relationships when creating replacement documents.
- Consult `docs/code/` before reconstructing codebase understanding from scratch.
- Maintain `docs/code/` when substantial implementation changes make its explanations inaccurate.
- Treat `docs/code/` as a current projection of the implementation, not as an immutable historical record.

---

# 25. Things an Agent Must Not Assume

Do not assume:

- every project is public;
- every project root is under `C:/lion/github/`;
- every project has an `examples/` directory;
- every document in `docs/` is authoritative;
- generated HTML is the source;
- `raw` means disposable;
- an empty `.zookeep/` is useless;
- Zoo's folder GUID is the same as every other project ID;
- repository name and package name are always identical;
- every task in `db/tasks.md` is approved for immediate execution;
- all old filename widths should be normalized;
- all metadata should be moved into `db/` immediately;
- a fashionable Python architecture is preferable to the project's existing procedural structure.

---

# 26. Improvement Agenda

The following improvements are desired across this project ecosystem.

## 26.1 Near-term, high-value

- Standardize document headers.
- Standardize `supersedes` and `superseded-by`.
- Standardize conversation and coordinate indexes.
- Clarify source-of-truth rules.
- Use `db/rules.md` more consistently.
- Use `db/tasks.md` more consistently.
- Ensure dependencies are declared.
- Ensure meaningful commands use `[project.scripts]`.
- Add obvious test runners.
- Keep READMEs accurate.

## 26.2 Medium-term structural work

- Define a shared schema or SoftSpec for project coordinates.
- Clarify conceptual project IDs versus folder IDs.
- Evolve Zoo metadata.
- Decide which metadata belongs in `db/`.
- Make projects discoverable across machines.
- Make task artifacts harvestable by Silk Road.
- Add machine-readable document relationship indexes.
- Add tooling to detect metadata disagreement.
- Add migration tools rather than relying on manual rewrites.

## 26.3 Long-term vision

- Visual project interfaces projected from simple files.
- Document timelines and supersession graphs.
- Cross-project search and orientation.
- Digital sherpas that gather tasks.
- Nightly AI work driven by explicit goals and safe boundaries.
- Morning reports with completed work, evidence, and links.
- A shared ecology in which agents can understand not only code, but the full history and operational meaning of a project.

---

# 27. Example Archetype

A new reusable Python infrastructure project might begin as:

```text
new-project/
├── db/
│   ├── rules.md
│   └── tasks.md
├── docs/
│   ├── raw/
│   │   ├── 001__conversations.json
│   │   └── 002__initial-project-write-up.md
│   └── architecture/
├── examples/
│   └── basic_example.py
├── src/
│   └── new_project/
│       ├── __init__.py
│       └── main.py
├── tests/
│   └── test_main.py
├── .gitignore
├── LICENSE
├── pyproject.toml
├── README.md
├── run-tests.bat
└── zoo-project.json
```

A private application may instead omit `examples/`, add operational modules and a `ui/` subpackage, and live under the machine's private code root.

---

# 28. Final Orientation

The `python-2026-03` archetype is best understood as a **living project habitat**.

Its folders separate different kinds of truth:

- `src/` contains executable implementation;
- `tests/` and `guitests/` contain behavioral checks;
- `examples/` shows what the system can do;
- `docs/raw/` preserves the stream of thought and source material;
- `docs/architecture/` preserves decisions;
- `docs/code/` preserves agent’s maintained understanding of current implementation;
- `docs/manual/` may hold maintained explanation;
- `db/` contains living operational facts;
- `README.md` faces the public;
- `pyproject.toml` defines packaging, dependencies, and commands;
- `zoo-project.json` places the folder inside Lion's larger project ecology.

The filesystem is intended to support continuity between Lion, Wing-Cat, Codex, Claude Code, future agents, and future versions of Lion himself.

An agent should therefore do more than make code pass.

It should understand where it is, preserve the project's memory, respect the distinction between source and projection, leave clear traces, and help the project become easier to re-enter next time.
