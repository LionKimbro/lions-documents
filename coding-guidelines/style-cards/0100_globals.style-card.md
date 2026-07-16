```
document-id: lionsphilosophyofprogramming.style-cards-2026.globals.v1
title: Coding Style Card -- Globals
date: 2026-07-07
chatgpt: https://chatgpt.com/c/6a4e12e5-1564-83e8-9fc8-6b65e2e9f421
uuid: 22d507d2-9b9a-478f-a86d-dc86cb6b6281
taguri: lionkimbro@gmail.com,2026-07-07:style-card/globals/v1
tags: programming guidelines global globals
document-type: reference
purpose: This guide explains how to use global state deliberately in Lion-style Python programs.
```

# Lion Programming Guide: Globals

## Purpose

The purpose of globals is not laziness. The purpose is to prevent needless plumbing.

This guide explains how to use global state deliberately in Lion-style Python programs.

Many programs avoid globals by passing the same context, managers, registries, configuration objects, and state bundles through function after function. The result is often worse than the thing it was trying to avoid: long argument lists, repeated forwarding, artificial singleton objects, unnecessary context wrappers, and layers of indirection that make simple code hard to read.

Lion-style programs start from a different belief:

A program has a shared world.

That shared world should be designed, named, visible, inspectable, and governed by clear rules. It should not be hidden behind ceremony, and it should not be passed like luggage through every function call.

Globals are appropriate when a value belongs to the program’s shared context.

Arguments are appropriate when a value varies from call to call.

The goal is to keep functions focused on the action they perform, without forcing every function to receive the whole world as an argument.

This style depends on discipline. Globals should have clear names, clear ownership, clear mutation rules, and clear use policies. The program should be observable through logs and debug views. Correctness should be verified with debug-mode health checks and consistency checks.

Globals are not a substitute for thinking.

They are a way of making the program’s shared context honest.


## Core Rule

Use globals when the value belongs to the program’s shared context.

Do not pass contextual state through every function.

Pass only what varies.


## Bundles, Collections, and Registers

Global containers usually fall into three different roles.


### Bundles

A bundle is a fixed-shape container of named program facts.

A bundle’s keys should be declared at initialization.

The values are often scalars:

```python
g = {
    "mode": "idle",
    "count": 0,
    "selected_id": None,
}
```

But a bundle value does not have to be a scalar.

A value belongs in a bundle when it represents one named fact about the program state:

```python
g["current_handler"] = handle_idle_mode
g["active_sort_fn"] = sort_by_name
g["selected_tool"] = "brush"
g["running"] = True
```

The important distinction is not scalar versus non-scalar.

The important distinction is:

* Is this one named fact about the program?
* Or is this an open collection, queue, cache, table, registry, or subsystem?

Single contextual values belong in bundles, even when the value is a function, object, tuple, enum, path, or other non-scalar value.


### Open Collections

Some global containers are open collections.

These are dictionaries or lists that are expected to grow dynamically during execution.

```python
widgets = {}
records = {}
routes = {}
events = []
images = {}
```

An open collection does not need to know all of its keys in advance.

Its purpose is to collect things.

For example, `records` might map record ids to record data, and
`widgets` might map widget names or ids to live Tk widgets.


### Registers

A register is a fixed set of working slots.

Registers are globally accessible, but they are not general global
memory. They are temporary working memory used for a specific
calculation, operation, traversal, or micro-context.

```python
scan_register = {
    "current_id": None,
    "current_record": None,
    "matches": None,
}

layout_register = {
    "parent": None,
    "row": None,
    "column": None,
}
```

Registers should be initialized with all expected keys.

Use `None` when there is no current value yet, or use whatever empty
value is appropriate for that slot.

Registers should have especially clear policy, because they are easy
to misuse. A register should answer:

* What operation uses this?
* When is it cleared?
* Who is allowed to write it?
* Is it temporary, derived, or part of a calculation?

The rule is:

Bundles hold known program facts.

Open collections gather many dynamic things.

Registers hold temporary working context.



## "g": The Main Global Bundle

The main global bundle is usually named `g`.

Use `g` for the central facts of the program’s shared context.

```python id="mfi7nh"
g = {
    "running": True,
    "mode": "idle",
    "selected_id": None,
    "current_handler": None,
}
```

`g` should give a reader a quick view of the program’s main state.

It is not the place for every global value.

Large collections, registries, caches, queues, widgets, records,
images, routes, and other major regions of the program should usually
have their own global containers.

Use `g` for the central dashboard, not the whole warehouse.


## Use Other Bundles When `g` Gets Crowded

`g` is the main global bundle, but it does not need to hold every
named program fact.

Use other bundles when a region of global state has its own theme.

```python
g = {
    "running": True,
    "mode": "idle",
    "selected_id": None,
}

h = {
    "last_error": None,
    "last_event": None,
    "debug_enabled": False,
}

gfx = {
    "canvas": None,
    "scale": 1.0,
    "origin_x": 0,
    "origin_y": 0,
}
```

These are still global bundles. They should still have fixed shape,
clear names, clear ownership, and clear use policies.

The reason to make another bundle is inspection.

In the debugger, each bundle should reveal one related part of the
program’s global space. `g` shows the main state. `h` may show
auxiliary state. A bundle like `gfx` may show the state of the
graphics subsystem.

Global bundle names should usually be short, because they are used
throughout the program.

But they should still be meaningful.

If `g` is getting crowded, make another bundle.

If `g` and `h` are both getting crowded, make another thematic bundle.

Do not make random bundles. Make named regions of the program’s shared
world.


## Mutate Globals; Do Not Rebind Them

Global containers should be mutated in place.

Good:

```python
g["mode"] = "running"
events.append(evt)
widgets["main_button"] = button
```

Avoid:

```python
global g
g = {}
```

In Lion-style Python, the `global` keyword should almost never be
needed because global containers are not rebound. They are modified.


## Give Every Global a Use Policy

A global is not “anything goes.”

Every global should have an implied or documented policy:

```python
g = {
    "running": True,      # controls main loop lifetime
    "mode": "idle",      # current application mode
}

widgets = {}             # Tk widgets by name
events = []              # append-only in-memory event log
records = {}             # canonical records by id
```

The question is not “Are globals allowed?”

The question is:

* Who writes this?
* Who reads this?
* Is it mutated in place?
* Is it part of the program’s shared world?
* Is it temporary, persistent, or derived?


## Keep Large or Volatile Data Separate

Do not overload `g` with everything.

Large data, caches, images, database rows, worker queues, GUI widgets,
and transient runtime structures usually deserve their own named
containers.

Good:

```python
g = {
    "mode": "idle",
    "selected_id": None,
}

images = {}
records = {}
widgets = {}
```

Bad:

```python
g = {
    "mode": "idle",
    "selected_id": None,
    "all_images": {},     <-- BAD
    "all_records": {},    <-- BAD
    "every_widget": {},   <-- BAD
}
```


Use `g` for the central dashboard, not the whole warehouse.


## Bad Use of Globals

Globals become bad when they are invisible, undocumented, surprising, or used to hide real variation.

Do not use global state to avoid passing a value that genuinely varies from call to call.

Bad:

```python
g["current_filename"] = filename
save()
```

Better:

```python
save_file(filename)
```

Do not casually rebind global containers:

```python
global config
config = load_new_config()
```

The rule is:

Context belongs in globals.

Variation belongs in arguments.


## Agent Instructions

When writing Lion-style code:

* Prefer a small number of visible global containers.
* Use `g` as the main bundle for central program facts.
* Use separate global containers for open collections such as
  `widgets`, `records`, `routes`, or `images`.
* Mutate global containers in place.
* Do not use the `global` keyword.
* Never pass configuration or shared application state through a function.
* Pass only what varies.
* Keep the program’s shared world inspectable.


## See Also

* **0410 Registers** — for how active values are held, updated, and used by nearby functions.

