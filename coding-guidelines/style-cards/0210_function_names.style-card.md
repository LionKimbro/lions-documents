```
document-id: lionsphilosophyofprogramming.style-cards-2026.function-names.v1
title: Coding Style Card -- Function Names
date: 2026-07-26
document-type: reference
tags: programming guidelines functions function names naming predicates handlers primitives
purpose: This guide explains how function names give Lion-style Python programs a clear procedural shape.
```

# Lion Programming Guide: Function Names

## Purpose

Function names should make the shape of a program visible.

A reader should be able to scan a module and see its entry points from
the outside world. When the reader looks at a function, they should be
able to quickly know whether it is an ordinary action, a one-off
procedural step, a predicate, or an internally called helper versus an
externally invoked handler. The naming system is not about making
every name uniformly short or uniformly precise. It is about giving
each kind of function the name shape that matches its role.

Lion-style code uses a small vocabulary for high-frequency operations,
straightforward names for reusable actions, and long descriptive names
when a function is really a labeled step in one particular procedure.

The result should read like a map rather than a maze.


## Core Rule

Choose a function name based on the function's role and expected use:

```text
high-frequency primitive       send()
reusable action                verb_object()
one-off procedural unit        long_descriptive_prose_like_name()
boolean question               is_..., has_..., should_..., or may_...
external callback handler      handle_...() or on_...()
internal helper                _name_using_the_same_rules()
```

Do not make a function name more general than the function really is.

Do not make a unique, local step pretend to be a reusable primitive.


## Primitives: One Word

Use a single-word name for a function called extremely frequently,
such as multiple times within 50 to 100 lines of code. These functions
form the core vocabulary of a small machine.

```python
log("connected")
send(msg)
recv()
push(obj)
pop()
flush()
_emit(evt)
```

Primitives should have minimal conceptual weight. Their surrounding
context does most of the disambiguation. Global state and flags may
select their precise behavior internally.

Do not use a one-word name merely to save space. A rare or specialized
operation needs a name that tells the reader what it does.


## Reusable Actions: `verb_object`

Most reusable functions should use a simple `verb_object` name.

```python
send_message(msg)
store_photo(data)
lookup_key(k)
process_event(evt)
save_text(text, ["file"])
```

Prefer simplicity over excessive precision. Context and flags can
refine an action without forcing a family of nearly identical names.

```python
save_text(text, ["file"])
save_text(text, ["cache"])
save_text(text, ["network"])
```

The function name should identify the stable action. The flags select
a small behavior variant. See **0220 Function Arguments and Flags**
for the flags convention.


## One-Off Functions: Long and Descriptive

When a function is intended to be called from one location only, give
it a long, prose-like, descriptive name.

The rule is about intended scope, not about whether the function is a
step inside one particular main procedure. A one-off function may be a
procedural step, a solitary callback, a special-case operation, or any
other narrow function with one source of invocation.

Ask:

```text
Is this meant for general use?
Or is it tight, narrow, and specific to one place?
```

Use the long name for the second case.

```python
rebuild_everything_from_disk_now()
do_initial_pipeline_routing_setup()
update_filetree_cache_after_reload()
check_for_restart_request_and_handle()
```

One-off procedural steps are a common use. They are often zero-argument
functions operating on current machine state, and divide a large
procedure into peer-sized, readable steps. But the long-name rule is
not limited to them.

They signal:

* This function is not intended as a general-purpose callable utility.
* Its precise role matters more than a compact general vocabulary.
* Its scope is tight, narrow, and specific.

Long names are appropriate here because the function is local to one
calling location; they do not clutter the program's general vocabulary.


## Predicates: Names That Read as Questions

Predicates return a boolean. Their names should read as true/false
questions and state whether they test condition, existence, policy, or
permission.

Predicate grammar overrides the ordinary `verb_object` rule. A
predicate is named as a question, not as an action. Its prefix is a
grammatical signal and does not count against the function's word
budget.

Use these prefixes:

```text
is_        state or condition
has_       possession or existence
should_    policy or intention
may_       permission, capability, or conditional allowance
```

### Reusable Predicates

Use short, uniform predicate names for system-wide checks used in more
than one place.

```python
is_system_ready()
is_stream_open()
is_message_received()
has_outbox_messages()
has_pending_uploads()
should_persist_now()
should_retry_later()
may_attempt_reconnect()
```

These are stable building blocks of program logic, so keep them short
and scannable. `is_message_received()` is valid as a reusable
predicate even though, after `is_`, it has the two-word shape
`message_received` rather than the ordinary action shape
`verb_object`.

### Single-Context Predicates

Use a long, descriptive predicate when the condition is tested from one
calling location and belongs to one specific procedural moment.

```python
is_this_the_first_time_the_application_has_started()
should_we_reload_everything_from_disk_now()
has_user_already_selected_a_photo_this_session()
may_we_proceed_with_initial_pairing_setup()
```

These are labeled decisions inside a procedure, not reusable system
vocabulary. For the three-or-more-word guideline, the prefixes `is_`,
`has_`, `should_`, and `may_` do not count as words.


## Callback Handlers: `handle_...()`

Callbacks are invoked by an external system or framework: Tkinter,
sockets, network protocols, timers, event loops, file watchers, or GUI
widgets. They are the edges where the outside world enters the program.

All callback handlers should begin with `handle_` or `on_`; prefer
`handle_` unless there is a local convention that makes `on_` clearer.

Callback handlers should also use a long, narrative name with at least
three meaningful words after the prefix.

```python
handle_when_user_clicks_the_pairing_button(event)
handle_when_the_socket_receives_a_packet(sock, data)
handle_when_the_camera_initialization_completes()
handle_after_receiving_mqtt_message_from_desktop(msg)
handle_if_user_confirms_file_overwrite()
```

The `handle_` or `on_` prefix does not count toward the three-word
guideline.

This makes callback entry points immediately visible in a function
list, documents the event they handle, and improves traceability in
stack traces.


## Internal Helpers

Internal helpers follow the same naming rules as public functions, but
begin with one underscore.

```python
_send()
_save_text_to_file()
_is_ready()
_handle_when_socket_closes()
```

The underscore marks implementation detail. It does not excuse a vague
or misleading name.


## Agent Instructions

When writing Lion-style code:

* Name high-frequency core operations with one word.
* Name most reusable actions with `verb_object`.
* Name functions intended for one calling location with long, narrative, descriptive names.
* Give boolean-returning functions an `is_`, `has_`, `should_`, or `may_` question name.
* Use short predicates for reusable checks and long predicates for one-flow decisions.
* Prefix external callback handlers with `handle_` or `on_`; prefer `handle_`.
* Give callback handlers three or more meaningful words after their prefix.
* Use flags to select small variants of a stable action instead of proliferating nearly identical function names.
* Prefix internal helpers with `_`, while keeping their names honest and role-shaped.
* Let a procedure's function names show the actual machine and its flow.


## See Also

* **0220 Function Arguments and Flags** — for arguments as caller choices and flags as behavior selectors.
