```
title: Coding Style Card -- Registers
date: 2026-07-08
chatgpt: https://chatgpt.com/c/6a4e1ab9-41f4-83e8-9b2d-c9eba271c3cd
uuid: d5f8dafb-3c5f-4b61-85e4-eeab46731a72
taguri: lionkimbro@gmail.com,2026-07-07:style-card/registers/v1
```

```yaml
title: Coding Style Card -- Registers
date: 2026-07-08
uuid:
taguri: lionkimbro@gmail.com,2026-07-08:style-card/registers/v1
```

# Lion Programming Guide: Registers

## Purpose

This guide explains how to use registers in Lion-style Python.

A register is a named working slot for the current procedural context.

Registers are useful when several nearby functions cooperate on the same current thing. They reduce needless argument plumbing, make the active context visible in the debugger, and let procedural code read as a clean sequence of operations.

Registers are not a replacement for arguments, records, local variables, or good data structures.

They are a tool for expressing current context.

## 1. Use Registers for Current Context

Use a register when a value has become the current subject, target, or working value of a procedural flow.

Example:

```python
reg["record"] = records[rid]

normalize_record()
validate_record()
save_record()
refresh_record_view()
```

Here, the record is not just one argument to one function.

It is the current subject of the whole flow.

The register says:

> These operations are about the current record.

Common registers include:

```python
reg["sel"]       # current selection
reg["record"]    # current record
reg["window"]    # current window
reg["event"]     # current event
reg["count"]     # count being built
reg["L"]         # current target list
reg["I"]         # current loop item or index
```

A register may hold an object, id, counter, event, current output target, or other active working value.

The point is not the type of value.

The point is the role:

> This is what the nearby procedure is currently working with.

Registers should usually be initialized with their expected keys.

```python
reg = {
    "sel": None,
    "record": None,
    "window": None,
    "event": None,
    "count": 0,
    "L": None,
    "I": None,
}
```

Keep register names short, but clear.

A programmer or coding agent should be able to inspect `reg` and understand what the active procedure is doing.

## 2. Make the Register Judgment Call

Do not use a register merely because it avoids an argument.

Use a register when it improves the shape of the code.

Before introducing a register, ask:

* Will several nearby functions use the same value?
* Is the value current context, rather than real variation?
* Would passing it repeatedly create plumbing noise?
* Would the procedural flow read better if this value were established once?
* Would the function names still make sense without the argument?
* Would inspecting `reg` make the active operation clearer?
* Is the register lifetime easy to understand?

Good:

```python
def edit_record(rid):
    reg["sel"] = rid
    reg["record"] = records[rid]

    load_record_fields()
    draw_record_editor()
    connect_record_buttons()
```

The handler establishes the current selection and current record. The following functions operate inside that context.

Bad:

```python
reg["path"] = path
write_text(text)
```

Better:

```python
write_text(path, text)
```

The path varies from call to call. It should be an argument.

The rule remains:

> Context belongs in globals.
> Variation belongs in arguments.

Also, do not explode good structures into registers.

Bad:

```python
reg["name"] = record["name"]
reg["status"] = record["status"]
reg["image_path"] = record["image_path"]
```

Better:

```python
reg["record"] = record
```

Let the record remain a record.

Use a register to say which record is current.

Register-based functions need clear names.

Good:

```python
normalize_record()
validate_record()
refresh_record_panel()
```

These names make the implied context obvious.

Bad:

```python
do_it()
handle()
process()
```

These hide too much.

A function that uses registers should read like an operation on the current context.

## 3. Keep Registers Clean and Safe

Registers require hygiene.

At the beginning of a register-based flow, set the registers the flow depends on.

```python
def draw_record_panel(rid):
    reg["sel"] = rid
    reg["record"] = records[rid]
    reg["L"] = []

    emit_record_title()
    emit_record_fields()
    emit_record_buttons()

    return reg["L"]
```

Setting a register redundantly is sometimes good.

It marks the beginning of the flow and tells the reader what the active context is.

Do not trust stale register values merely because they are probably still correct.

Set what the flow needs.

A register may also target an existing output list:

```python
def target_list(L):
    reg["L"] = L

def build_lines():
    emit_header()
    emit_body()
    emit_footer()
```

Here, `reg["L"]` means:

> This is the list currently receiving output.

If the code always writes to one specific global list, use that list directly. Use `reg["L"]` when the output target is current context.

A register may also be used as a loop variable when the loop body is a procedural flow:

```python
for reg["record"] in selected_records:
    normalize_record()
    validate_record()
    save_record()
```

This means the called operations work on the current record.

Do not use register loop variables for ordinary iteration where a local variable is clearer.

Registers are for active, nearby, procedural control.

Do not trust them across:

* threads
* delayed callbacks
* `.after()` calls
* recursive calls
* uncontrolled re-entry
* code that may run after the current flow has ended

Bad:

```python
reg["record"] = record
schedule_later(save_current_record)
```

By the time `save_current_record()` runs, `reg["record"]` may refer to something else.

Package the needed value explicitly:

```python
schedule_later(lambda: save_record(record))
```

For threads, do not communicate through registers. Send an explicit work packet.

For recursion, do not use a plain register unless there is an explicit restoration strategy.

Clear a register when stale context would be dangerous or confusing.

```python
def process_record(rid):
    reg["record"] = records[rid]

    normalize_record()
    validate_record()
    save_record()

    reg["record"] = None
```

Clearing is not always necessary.

Clean registers are registers that contain what the active flow says they contain.

## Where Registers Pay Off

Registers are especially useful when a program has a strong current working context.

They have helped enormously in these kinds of systems:

### Turtle-like drawing systems

Drawing systems often have a current position, direction, pen, color, scale, or target surface.

Registers make these current drawing facts visible.

```python
reg["x"] = 100
reg["y"] = 200
reg["angle"] = 90
reg["pen"] = "black"
```

Then small drawing operations can act on the current drawing context.

```python
forward()
turn_left()
draw_label()
```

This style can make drawing code read like direct instructions to a little drawing machine.

### Statistics over parses and explorations

When parsing, scanning, walking, or exploring a structure, registers are useful for current counters, current matches, current totals, and current observations.

```python
reg["count"] = 0
reg["matches"] = 0
reg["depth"] = 0
```

Nearby operations can update the current statistics without passing the same accumulator through every function.

This is useful when the statistics belong to the current exploration, not to one isolated function call.

### 2-D and 3-D transform systems

2-D and 3-D drawing systems gain a lot from register-based interaction.

Transform code often has a current position, rectangle, scale, zoom, matrix, viewport, or coordinate frame.

```python
reg["x"] = 0
reg["y"] = 0
reg["rect"] = (x0, y0, x1, y1)
reg["zoom"] = 1.0
reg["matrix"] = current_matrix
```

Many small operations can then work against the current transform context.

```python
move_to_origin()
apply_zoom()
project_rect()
draw_current_rect()
```

This avoids dragging coordinate state through every operation.

It also makes the current geometric context easy to inspect in the debugger.

For graphical systems, this can be a major clarity gain.

### File navigation processes

Registers can also help when navigating inside a file.

For example, a binary search through a file may keep the file and current index in registers.

```python
reg["f"] = f
reg["I"] = index
```

Then small operations can act on the current file and current position.

```python
seek_current()
read_current_record()
compare_current_record()
move_left()
move_right()
```

The file and index form the micro-context.

Once that context is registered, the navigation operations can become small, direct instructions.

This can make file search and file exploration code much clearer.


## Agent Instructions

When writing Lion-style code:

* Use `reg` for temporary working context.
* Initialize expected register keys.
* Use registers when several nearby functions share the same current value.
* Do not introduce a register just to remove one argument.
* Do not avoid a register when repeated context passing creates plumbing noise.
* Use arguments for real variation.
* Keep records and dictionaries intact; do not explode them into registers.
* Establish registers at the beginning of a flow.
* Set registers redundantly when doing so clarifies the active context.
* Use register loop variables only when nearby operations share the loop context.
* Use `reg["L"]` for a current output target, not as a random list.
* Do not trust registers across threads, delayed callbacks, recursion, or re-entry.
* Clear registers when stale context would be dangerous.
* Keep register-based function names clear enough that the implied context is obvious.

## See Also

* **0100 Globals and Bundles** — for the distinction between bundles, open collections, and registers.
* **0220 Function Arguments** — for deciding what belongs in arguments versus shared context.
* **0415 Registers with Tkinter** — for callback, `.after()`, event, and projection hazards.
* **0420 Other Machine Parts** — for stacks, return stacks, indexed memory, and data tapes.

