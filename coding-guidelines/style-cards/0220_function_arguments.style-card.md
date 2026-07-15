```
document-id: lionsphilosophyofprogramming.style-cards-2026.function-arguments.v1
title: Coding Style Card -- Function Arguments and Flags
date: 2026-07-07
chatgpt: https://chatgpt.com/c/6a4dee3a-0514-83e8-8015-b8e50fc89cab
uuid: 567d4f5c-11d5-465d-97b4-15b47f769a17
taguri: lionkimbro@gmail.com,2026-07-07:style-card/function-arguments-and-flags/v1
```

# Lion Programming Guide: Function Arguments and Flags


## Purpose

This guide explains how function arguments work in Lion-style Python.

The purpose is to make function calls express intention.

A function call should show what is being chosen, changed, selected,
moved, saved, rendered, or transformed.

It should not show the plumbing required to make the program operate.

The programmer should be able to look at the function call and
immediately recognize the meaningful action.


## Theory

Parameters should express the real degrees of freedom of the function.

A parameter is legitimate when it represents something the caller is
actually choosing.

An argument varies when different calls to the same function naturally
provide different values.

```python
select_record("A104")
select_record("B219")
select_record("C002")
```

Here, the record id varies. The caller is choosing which record to
select. That is a real argument.

```python
set_value("status", "ready")
set_value("status", "done")
set_value("mode", "edit")
```

Here, the key and value vary. The caller is choosing what field
changes and what it changes to. These are real arguments.

A value does not become an argument merely because the function needs
access to it.

Do not pass databases, widget maps, config objects, logger objects,
state bundles, or other stable program infrastructure through function
calls merely because the function needs access to them.

That is context.

Context is the stable surrounding situation in which the function
runs.

Context includes the current database, the current window, the current
mode, the widget registry, the configuration, the logger, the selected
record, and the shared program state.

These are facts already established by the program. The caller is not
choosing them at the moment of the call.

Context belongs in the global context.

Arguments are reserved for real variation.

A Lion-style function call exposes the decision being made. It does
not expose the pipes behind the wall.


## Note: Changing Is Not Choosing

A value changing over time is not the same thing as the caller
choosing that value.

For example, the current inbox entries may change every time the inbox
is scanned. That does not mean entries should be passed to the
function that processes the current queue.

Bad:

```python
entries = scan_inbox()
process_entries(entries)
```

This suggests callers may provide any arbitrary entries list they
want. But the intended operation is not "process this caller-selected
list." The intended operation is "process the program's current
scanned inbox."

Good:

```python
entries = []

scan_inbox()
process_entries()
```

In this shape, `entries` is an open global collection. `scan_inbox()`
updates the machine's current queue, and `process_entries()` acts on
that current queue.

The queue changes over time, but it is still machine state, not a
caller-chosen argument.


## Policy

Lion-style functions have 0, 1, or 2 ordinary arguments.

A function may also accept one auxiliary flags argument.

The ordinary argument shapes are:

```text
0 arguments
1 argument
2 arguments
```

The expanded shapes are:

```python
refresh()
select_record(rid)
set_value(key, value)

save_text(text, flags)
set_color(name, value, flags)
```

The flags argument is not ordinary data.

It is an auxiliary behavior selector.


## Argument Shapes

### 0 Arguments: Direct Command

Use zero arguments when the function acts on the current program context.

```python
refresh_window()
save_file()
reset_connection()
poll_jobs()
render_canvas()
```

These calls read as direct commands.

The program already knows what is being refreshed, saved, reset, polled, or rendered.

A zero-argument function is called a nullary function.


### 1 Argument: Command With Variation

Use one argument when one thing varies.

```python
select_record(rid)
delete_file(path)
render_item(item_id)
say(msg)
```

The function still reads as a command, but the caller supplies the one changing thing.


### 2 Arguments: Paired Variation

Use two arguments when the values form a natural pair.

```python
set_value(key, value)
move_item(src, dst)
write_file(path, text)
rename_item(old_name, new_name)
```

Two arguments are acceptable when the pair is the meaningful action.

If the second value is not part of a natural pair, reconsider the function.


### Flags: Variant Selection

A function may accept one auxiliary flags argument.

The flags argument is a list of strings.

Each string is one flag.

```python
save_text(text, ["file"])
save_text(text, ["file", "cache"])
save_text(text, ["cache", "network"])
```

Each flag is written as a single lowercase word, or as two lowercase words joined with a hyphen.

```text
file
cache
network
fit-width
actual-size
overwrite-file
```

Flags select variants of the command.

They are useful when several small behavioral choices may be combined.

A flag answers a small variant question:

```text
Which destination?
Which mode?
Which treatment?
Which stage?
Which optional behavior?
```

Flags must not carry context.

Good:

```python
save_text(text, ["file", "cache"])
render_image(img, ["fit-width"])
open_window(name, ["modal"])
```

Bad:

```python
save_text(text, [database])
render_image(img, [widgets])
open_window(name, [config])
```

The first group selects variants.

The second group passes plumbing.

That is not Lion-style.


## Dictionary Arguments

A dictionary may be used as a single argument.

This is allowed when the dictionary represents one coherent thing.

Examples:

```python
start_program(profile)
set_context(ctx)
run_job(job)
handle_event(event)
load_record(record)
apply_patch(patch)
```

In these examples, the dictionary is not a pile of loose parameters.

It is a record.

The function receives the record as one thing and treats it as one thing.

This is common when starting a program, loading a configuration profile, accepting a job request, handling an event, installing context, or passing around an evolving data structure.

Good:

```python
profile = {
    "program_name": "stickerdb",
    "root_path": "C:/lion/stickerdb",
    "database_path": "C:/lion/stickerdb/data.json",
    "window_title": "StickerDB",
}

start_program(profile)
```

Here, `profile` is the thing being handed to the function.

The caller is choosing which profile to start with.

That is real variation.

Good:

```python
ctx = {
    "records": records,
    "widgets": widgets,
    "mode": "edit",
    "selected_id": None,
}

set_context(ctx)
```

Here, the function call is explicitly about installing context.

The context dictionary is not being dragged through the program as plumbing. It is the subject of the call.

Bad:

```python
display({
    "x": x,
    "width": width,
    "height": height,
    "font": font,
    "fontsize": fontsize,
    "device": device,
    "color": color,
    "mode": mode,
})
```

This is not one real argument.

It is many arguments hidden inside a dictionary.

A dictionary argument is legitimate when it forms a meaningful record:

```python
set_device(window_context)
set_mode("immediate")

draw_object({
    "x": 30,
    "y": 50,
    "text": "Hello, world!",
    "color": "blue",
    "font": {
        "font": "helvetica",
        "font-size": "12pt",
    },
})
```

The dictionary is not plumbing. It is the thing being drawn.

A dictionary is allowed when it is a record with identity in the problem domain.


## Agent Instructions

When writing Lion-style code:

* Use 0, 1, or 2 ordinary arguments.
* Add one optional `flags` argument only when variant selection is needed.
* Make `flags` a list of string flags.
* Do not pass stable program infrastructure through function calls.
* Context belongs in the global context.
* Remember that changing is not choosing; current queues, selections, modes, and clocks may change over time without becoming caller-chosen arguments.
* Do not create false degrees of freedom by accepting a parameter for machine state that should live in a global bundle, open collection, or register.
* Pass dictionaries only when they represent one coherent record.
* If a function wants many arguments, stop and redesign.

For each parameter, ask:
- Is the caller meant to choose this value?
- Or is this current machine state?
- Would accepting this parameter imply an API affordance we do not want?


## See Also

* **0100 Globals and Bundles** — for how stable program context is stored and organized.
* **0410 Registers** — for how active values are held, updated, and used by nearby functions.

