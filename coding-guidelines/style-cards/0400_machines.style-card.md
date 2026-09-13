```
document-id: lionsphilosophyofprogramming.style-cards-2026.machines.v2
title: Coding Style Card -- Machines
date: 2026-08-24
uuid: 044edafb-c4c2-451d-9e72-761e159fb4bc
series-uuid: 6205a832-7967-4cd3-b5d5-ec19c8e92977
taguri: lionkimbro@gmail.com,2026-08-24:style-card/machines/v2
document-type: reference
tags: programming guidelines machines machine
purpose: This guide explains how to write modules that are shaped like a little machine that the program needs.
reference-chatgpt: https://chatgpt.com/c/6a8c0c45-fdac-83e8-83cb-786e49a09adc
caution: This work remains very much in progress.
```

# Lion Programming Style — Short Form

Think of the program as a staged machine whose state evolves under
explicit control, not as a collection of objects which own behavior.

Prefer **machine-oriented procedural code** over object-oriented design.

Write the program as a set of **machines that operate on data**, the
output of one machine becoming the input of another.

Do not model ordinary data as active objects merely because the data
has identity or structure.  The data does not do anything.  The
machine does everything to the data.

Ask:

> What process is this code performing?

Then expose that process directly.

Prefer:

```python
load_current_record()
normalize_record()
update_indexes()
render_record()
```

over distributing those operations into methods on domain objects.

Create NO Classes.  Model the data with dictionaries, lists, and
global values, global registers.  Manipulate the data with functions.
Group functions and global data (including registers) in Python
modules.

Do not create classes to:
* group functions
* hold mutable state
* avoid globals
* create namespaces
* imitate domain nouns
* encapsulate a record
* satisfy conventional OOP style

Prefer:

```python
record = {
    "id": rid,
    "name": name,
    "status": status,
}
records.append(record)
```

over:

```python
record = Record(...)
```


## When One Can Use a Class

The only time you can use a class is when it represents a tiny,
general-purpose computational abstraction whose behavior is intrinsic
to the value itself -- not when it represents a piece of the program's
machinery.

A Matrix can legitimately know what matrix multiplication means; The
operation belongs to the mathematical object.  A Box can legitimately
mean "a mutable cell containing one value."  A list can legitimately
know how to append, index, iterate, and so on.  These are small
algebraic/data abstractions.  You could pick them up and carry them
into ten thousand unrelated programs without bringing any application
architecture along with them.

But ProjectManager, PanelManager, EventRouter, ApplicationController,
DatabaseCoordinator, -- those aren't really things.  They're chunks of
procedure dressed up as nouns.

A good class has something like an intrinsic law:

Matrix x Matrix -> Matrix
Box.value -> contained value
List.append(x) -> modified sequence

A bad class has methods because somebody needed somewhere to put code:

PanelManager.refresh_panels()
PanelManager.handle_click()
PanelManager.load_layout()
PanelManager.save_layout()
PanelManager.update_state()

At this point, the class isn't modeling a coherent reusable
object. It's become a control-flow container.  And worse, it starts
smuggling in ownership: "the Manager owns the panels; therefore state
lives here; therefore methods become the privileged way to reach that
state; therefore execution gets distributed among object
relationships."

Use a class ONLY when data and operations form a small,
self-contained, ultra-reusable abstraction whose semantics exist
independently of the application.  Never use classes as an
architectural decomposition mechanism.

If the word "Manager" appears ANYWHERE in the name of a class, it
means "I turned a region of the program into an object," and that's
grounds for IMMEDIATE EXILE.

"I never knew you."

...

If this all seems rather complex and abstract, just go with:
NO CLASSES.

note: https://chatgpt.com/c/6a8c273f-e5dc-83e8-a4ea-86c10e1ab180
  -- one day, I'll work all this in, to a specific guide ABOUT classes specifically,
     and my division points from OOP, and why it's center of gravity is all wrong
     for my ethos

---

## Organize By Process, Not By Noun

Do not ask:

> What does this object do?

Ask:

> What system operates on this data?

Do not distribute behavior merely according to which noun appears in the operation.

Prefer:

```python
handle_collision(a, b)
```

inside a collision system over inventing ownership such as:

```python
a.collide_with(b)
b.receive_collision(a)
```

Interactions often belong to the **system handling the interaction**, not to either participant.

---

## Think Like ECS

A datum may participate in many different systems.

A game entity may participate in:

```text
movement
collision
rendering
damage
persistence
```

through different aspects of its data.

Do not force all of those behaviors into one object.

Prefer systems that systematically operate on the relevant data.

Think:

```text
movement system operates on Position + Velocity
collision system operates on Position + Collider
render system operates on Position + Renderable
```

The same datum may mean different things to different machines.

---

## Control Time Explicitly

Lion cares strongly about **when operations happen**.

Do not assume that a request for an operation means the operation should execute immediately.

Separate:

```text
request time
```

from:

```text
execution time
```

When a request arrives at an arbitrary time, prefer turning it into data:

```python
save_requests.append(request)
```

or:

```python
g["save-requested"] = True
```

Then process that request at the stage where the operation belongs.

Do not casually write:

```python
if everything_is_ready():
    save()
```

when the machine can instead guarantee that persistence occurs only at a stage where its assumptions already hold.

Prefer **structural scheduling** over arbitrary-time readiness checks.

---

## Preserve Legal System States

Think carefully about what states the system is allowed to occupy.

The system should move through controlled transitions between legal states.

Temporary inconsistency may exist inside a known transition stage, but should not casually leak into the rest of the program.

For example:

```text
mutate
reconcile
rebuild derived state
restore invariants
stable boundary
persist
```

Prefer:

> operations that occur where their preconditions hold by construction

over:

> operations callable anywhere that repeatedly ask whether their preconditions happen to hold.

A stage should establish the assumptions required by the next stage.

---

## Make The Main Process Visible

Top-level procedures should read like the machine operating.

Prefer:

```python
def execute():
    receive_requests()
    apply_changes()
    reconcile_state()
    rebuild_indexes()
    process_persistence()
    emit_updates()
```

Avoid hiding the operating sequence behind large networks of object calls.

A reader should be able to see:

```text
what happens
in what order
on what current state
```

---

## Prefer Visible State Over Hidden Ceremony

Machine state should be easy to inspect in a debugger.

A small dictionary such as:

```python
g = {
    "mode": "idle",
    "current-request": None,
    "save-requested": False,
    "state-stable": True,
}
```

is often preferable to equivalent state distributed through opaque object graphs.

The goal is not minimal state.

The goal is **visible, governed state**.

---

## Default Design Questions

Before designing code, ask:

```text
What machine is this?

What process does it perform?

What stages does that process have?

What is the current machine state?

What data is merely being operated upon?

What requests can arrive out of order?

Which requests should become queued data?

When should their effects actually execute?

What legal states may the system occupy?

What invariants hold at each stage boundary?

What interactions belong to a system rather than to either participant?

Which values are real caller choices?

Which values are merely shared context?

Am I inventing an object because the problem contains a noun?

Am I hiding process behind methods?

Am I making the code more general than the actual machine?
```

---

## Default Bias

When uncertain, bias toward:

```text
data structures over domain objects
modules over classes
functions over methods
systems over object behavior
explicit process over distributed control
queues and flags over arbitrary-time effects
staged execution over readiness checks
legal-state transitions over unrestricted mutation
visible shared context over parameter plumbing
exact machinery over generic architecture
```

The objective is not procedural programming for its own sake.

The objective is to make the program read like **the actual machine that performs the work**.
