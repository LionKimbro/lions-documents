```
document-id: lionsphilosophyofprogramming.style-cards-2026.machines.v1
title: Coding Style Card -- Machines
date: 2026-07-09
uuid: 8f3d12f9-3432-48a2-90fb-0a82add07e7f
taguri: lionkimbro@gmail.com,2026-07-09:style-card/machines/v1
document-type: reference
tags: programming guidelines machines machine
purpose: This guide explains how to write modules that are shaped like a little machine that the program needs.
```

# Lion Programming Guide: Machines

## Purpose

This card describes a deeper goal behind Lion-style globals,
registers, and function arguments.

The goal is not to use globals for their own sake.

The goal is to write modules that are shaped like the actual machine
the program needs.

A good module should feel exact. It should not pretend to be more
general than it is. It should not turn one concrete operation into a
fake library of reusable utilities. It should expose the current state
of the operation, then name the steps that act on that state.

This card is an early attempt to describe that style.


## Core Idea

A module can be a little machine.

That means:

* The module has a small visible state panel.
* The module performs one kind of work or owns one region of behavior.
* External input is unpacked once at the boundary.
* Internal functions act on the module's current state.
* Function names describe machine actions.
* Parameters are used only for real caller choices.

The module is not trying to be abstract.

The module is trying to be exact.


## Exactness Over Fake Generality

Sophisticated coding agents often try to make code cleaner by making
it more general.

Sometimes this is correct.

But often it creates fake generality: functions that accept records,
contexts, work packets, options, or state bundles even though the
system has no intended caller choice there.

Bad:

```python
def execute_round_corners(request):
    work = {
        "source": request["input"]["image_path"],
        "output": request["output"]["image_path"],
        "radius_percent": request["options"].get("radius_percent", 5),
    }
    read_image_size(work)
    round_corners(work)
    write_outcome(work)
```

This looks explicit, but it turns the operation into a fake generic
pipeline. The `work` packet is passed around because the functions need
access to it, not because each call is choosing a different work packet.

Better:

```python
g = {
    "request": None,
    "source": None,
    "output": None,
    "radius-percent": None,
    "width": None,
    "height": None,
}

def execute_round_corners():
    unpack_request()
    read_image_size()
    round_corners()
    set_success_outcome()
```

Here, the module is shaped like the actual operation. It has a current
job. The helper functions are not advertised as arbitrary utilities.
They are parts of the round-corners machine.


## Boundary First, Then Current State

External input should be normalized or unpacked at the boundary.

After that, the module interior may operate on current state.

Example:

```python
def execute_round_corners():
    unpack_request()
    read_image_size()
    round_corners()
    set_success_outcome()


def unpack_request():
    g["request"] = core.reg["request"]
    g["source"] = g["request"]["input"]["image_path"]
    g["output"] = g["request"]["output"]["image_path"]
    g["radius-percent"] = normalize_radius_percent(g["request"]["options"].get("radius_percent", 5))
```

`unpack_request()` is the boundary step.

After it runs, `g["source"]`, `g["output"]`, and
`g["radius-percent"]` are the current machine state.

The later functions do not need these facts as parameters.


## Machine State Is Not Hidden State

Global state is dangerous when it is invisible, sprawling, or
surprising.

Machine state should be the opposite:

* small
* named
* initialized
* easy to inspect
* owned by the module
* reset or overwritten at the beginning of the operation

Good:

```python
g = {
    "request": None,
    "source": None,
    "output": None,
    "width": None,
    "height": None,
}
```

This is not a junk drawer.

It is a control panel.


## Function Roles

Inside a machine module, functions usually have one of these roles:

* boundary functions unpack or normalize external input
* action functions perform one step using current state
* observation functions read or measure something and store the result
* output functions write final state, files, messages, or outcomes
* registration functions connect the module to the larger system

These functions often have zero arguments because the module already
knows its current subject.

Good:

```python
unpack_request()
read_image_size()
round_corners()
set_success_outcome()
```

Bad:

```python
read_image_size(work)
round_corners(work)
set_success_outcome(work)
```

The second version is only better if `work` is genuinely selected by
the caller. If `work` is merely the current operation state, passing it
around adds noise and creates false generality.


## Relationship To Other Cards

This card depends on:

* **0100 Globals**: machine state often lives in a module `g` bundle or
  other named global containers.
* **0220 Function Arguments**: arguments are for caller choices, not
  current machine state.
* **0410 Registers**: registers hold active working context that spans
  nearby functions.

The machine idea is what these techniques are for.

They are not separate tricks.

They are ways to make a module's real operating shape visible.


## Agent Instructions

When writing or refactoring Lion-style code:

* Ask what little machine this module actually is.
* Do not make the code more general than the system actually needs.
* Prefer a small explicit `g` bundle for module-owned current state.
* Unpack external input once at the boundary, then operate on current state.
* Use zero-argument internal functions when they are actions of the current machine.
* Do not pass work packets, config, queues, current records, or current requests merely because helper functions need access to them.
* Keep generic utilities only when there is real reuse or real caller choice.
* Make the main procedure read like the machine doing its work.


## Potential Agent Audit Instructions

Audit this code for Lion-style function arguments and machine modules.
For every function parameter, classify it as:
- caller choice
- coherent record
- flags
- current machine state
- plumbing/context
- false degree of freedom

Then refactor every parameter classified as current machine state,
plumbing/context, or false degree of freedom into an appropriate global bundle,
open collection, or register.

For every module, identify the little machine it represents, its current state
panel, its boundary unpacking functions, and its main procedural flow. Refactor
modules that pass work packets around internally when a module g bundle would
make the current operation clearer.


## Signs The Machine Is Clear

The code is probably moving in the right direction when:

* the top-level procedure reads as a short sequence of named steps
* the module state can be inspected in one small dictionary
* helper function names are specific and honest
* function signatures are short
* there are few courier variables passed from step to step
* the module does not pretend to be a general-purpose library
* the code feels like this exact operation, not an abstraction of many possible operations

