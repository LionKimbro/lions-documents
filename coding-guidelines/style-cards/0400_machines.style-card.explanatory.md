```
document-id: lionsphilosophyofprogramming.style-cards-2026.machines.v2.explanatory-version
title: Coding Style Card -- Machines
date: 2026-08-24
uuid: 46f6151e-e622-4dce-8212-c0766afa6245
series-uuid: 79a3945a-0874-43af-b9e5-f5882a67e819
taguri: lionkimbro@gmail.com,2026-08-24:style-card/machines/v2/explanatory-version
document-type: reference
tags: programming guidelines machines machine
purpose: This guide explains how to write modules that are shaped like a little machine that the program needs.
reference-chatgpt: https://chatgpt.com/c/6a8c0c45-fdac-83e8-83cb-786e49a09adc
```

# Lion Programming Guide: Machines

## Purpose

Machine coding organizes software around **processes performed by a system**.

The central question is not:

> What objects exist, and what behaviors belong to each object?

The central question is:

> **What process is this program performing?**

And then:

> What stage is the process in?
> What state does the process currently depend on?
> What requests have arrived?
> Which interactions must be handled?
> What operations are appropriate at this stage?
> What must happen before the machine may advance?

A machine is not merely “an object implemented with globals and functions.”

A machine is a different way of deciding **where agency, behavior, state, and time belong in a program**.

The data does not do the work.

**The system does the data.**

---

## Object-Oriented Thinking and Machine Thinking

Object-oriented programming commonly begins by identifying entities:

```text
Document
Panel
Connection
User
Message
Sprite
```

It then asks what state and behavior belong to those entities.

That naturally leads to code such as:

```python
document.save()
panel.render()
connection.send()
sprite.collide(other_sprite)
```

There is nothing inherently invalid about such code.

The important difference is the **organizing question**.

Object-oriented design tends to ask:

> **What thing owns this behavior?**

Machine coding asks:

> **What process is occurring, and where in that process does this operation belong?**

These are not the same question.

---

## The Data Is Not the Actor

A record may represent a document.

A dictionary may represent a panel.

A structure may represent a sprite.

That does not mean the document, panel, or sprite must become an actor in the architecture.

Machine coding generally treats data as **state operated upon by systems**.

Instead of imagining:

```python
panel.render()
panel.save()
panel.handle_click()
```

think:

```python
render_panels()
process_panel_events()
persist_layout_state()
```

The distinction is not merely syntactic.

The important question is:

> **Where does the operation actually belong in the running system?**

Rendering may belong to the rendering stage.

Persistence may belong to the persistence stage.

Input handling may belong to the event-processing stage.

The fact that all three operations concern a panel does not imply that all three should belong to a conceptual `Panel` actor.

---

## Behavior Does Not Have To Belong To A Noun

Object-oriented design often creates an ownership question:

```text
Does A affect B?

Does B receive A?

Should this be:

    a.collide(b)

or:

    b.receive_collision(a)
```

Machine coding often rejects the premise of the question.

A collision is not necessarily behavior belonging to A or behavior belonging to B.

It may simply be:

> **an interaction discovered and handled by the collision system during the collision-processing stage.**

For example:

```python
def run_collision_stage():
    find_collisions()
    resolve_collisions()
    apply_collision_results()
```

Inside that process:

```python
handle_collision(a, b)
```

may update A, B, spatial indexes, effects, scores, sounds, damage records, or other parts of system state.

The collision belongs to the **system process**, not metaphysically to either participant.

Machine coding therefore tends to organize behavior by **operational locality** rather than object ownership.

Functions belong together because they participate in the same mechanism.

And "while we are here," let's note that ECS is a useful example of machine coding because an entity is not asked to own all behavior associated with itself. Instead, it participates through different components in different systems, and each system systematically operates on the aspect of the world it governs.  The entity is one thing in the data model, but many different things to many different machines.

---

## The Machine Is A Process Through Time

A machine is not merely a collection of functions operating on shared state.

A machine has **temporal structure**.

The program proceeds through stages.

For example:

```text
receive input
    ↓
record requests
    ↓
apply mutations
    ↓
reconcile related state
    ↓
rebuild derived state
    ↓
reach stable boundary
    ↓
perform persistence
    ↓
emit output
```

The stages matter.

An operation that is valid during one stage may be invalid during another.

Machine coding therefore cares strongly about:

> **WHEN does this operation happen?**

This is one of the central differences between machine coding and ordinary object-oriented thinking.

A method call often makes an operation appear locally and immediately available:

```python
obj.save()
```

The call visually suggests:

> Here is an object. Saving is something this object can do. Do it now.

But persistence may not actually be a local operation.

The object may participate in a larger network of state.

Other records may refer to it.

Indexes may need rebuilding.

Derived state may still be changing.

A worker may still be producing results.

Another subsystem may still need to reconcile the mutation.

The meaningful operation may therefore be:

> **Persist the system after the relevant state has stabilized.**

That is a system operation.

---

## Legal System States

Machine coding treats the program as moving through a constrained set of **legal system states**.

The system should not be thought of as an arbitrary collection of values that happen to be present at one instant.

Its state has structure.

Certain relationships must hold.

Certain combinations are permitted.

Certain combinations are forbidden.

Certain temporary inconsistencies may be allowed only inside a specific transition stage.

For example, a mutation stage may temporarily create a state in which:

```text
primary data has changed
derived indexes are stale
cached values are no longer trustworthy
persistence is not yet allowed
```

That intermediate condition may be acceptable **inside the mutation/reconciliation process**.

But it is not a legal stable state of the whole system.

The machine must continue:

```text
mutate
    ↓
reconcile
    ↓
rebuild derived state
    ↓
restore invariants
    ↓
stable legal state
```

Only then may later stages depend on those invariants.

This is why stage boundaries matter.

A stage boundary can mean:

> **The machine now guarantees a particular set of invariants.**

Operations scheduled after that boundary may rely on those guarantees without rediscovering them from scratch.

The goal is not to ask arbitrary code:

```python
is_the_system_valid_right_now()
```

The goal is to know:

> **We are at a point in the process where this class of state is guaranteed to be legal.**

Machine coding therefore treats correctness partly as a property of the system's allowed transitions.

A good machine should make it clear:

```text
What states are legal?
What temporary states are allowed during a transition?
Which stage may create them?
Which stage must resolve them?
What invariants are guaranteed at each boundary?
Which operations are permitted in each state?
```

The machine should control movement through this state space.

It should not allow arbitrary parts of the program to produce arbitrary combinations of state and then rely on defensive checks everywhere else.

A useful rule is:

> **Prefer constrained transitions between known states over unrestricted mutation followed by validation.**

And another:

> **A stage should not merely perform work. It should establish the conditions under which the next stage is allowed to run.**

---

## Request Time Is Not Execution Time

External events arrive whenever they arrive.

A user clicks a button.

A network packet arrives.

A timer expires.

A worker finishes.

A subsystem requests persistence.

A component becomes dirty.

Machine coding does not assume that the requested effect should occur at the instant the request arrives.

Instead:

> **Requests become data.**

For example:

```python
save_requests.append(request)
```

or:

```python
g["save-requested"] = True
```

The requesting code records an obligation.

It does not necessarily perform the requested operation.

Later, the machine reaches the stage where that operation belongs:

```python
def run_persistence_stage():
    process_save_requests()
```

This separates:

```text
REQUEST TIME
```

from:

```text
EXECUTION TIME
```

The separation is deliberate.

Requests may arrive out of order with respect to the conditions required to fulfill them correctly.

The machine funnels those requests into the stage where their assumptions are valid.

---

## Do Not Ask "Can I Do This Right Now?"

Suppose arbitrary code receives a persistence request and does this:

```python
if everything_is_ready():
    save()
else:
    ...
```

This appears defensive.

But it creates a dangerous problem.

`everything_is_ready()` must now determine whether all assumptions required for persistence happen to be true at an arbitrary instant.

It may need to know:

```text
Are all mutations complete?
Are derived structures current?
Are queues drained?
Are callbacks still pending?
Is another subsystem midway through an update?
Has a worker completed?
Are relationships coherent?
Will something mutate the state immediately after this check?
```

The caller is asking a very difficult global question from a location that may have almost no structural knowledge of time.

Machine coding prefers a different solution:

> **Do not repeatedly test whether arbitrary moments happen to be safe.
> Arrange the process so the operation runs at a stage that is safe by construction.**

Instead of:

```python
if persistence_is_safe_right_now():
    persist()
```

prefer a process shaped like:

```text
perform mutations
perform reconciliation
finish dependent work

-------- stable boundary --------

process persistence requests
```

At the persistence stage, the program does not need to rediscover the temporal structure of the entire system.

The structure of the machine already establishes the relevant assumptions.

---

## Establish Correctness By Scheduling

This is an important machine-coding principle:

> **Prefer structural scheduling over arbitrary-time permission checking.**

If an operation requires certain invariants, place the operation in a stage where those invariants are established.

Do not scatter code throughout the program asking whether the invariants happen to hold right now.

This changes the architecture from:

```text
request
    ↓
check everything
    ↓
maybe perform effect
```

to:

```text
request
    ↓
record obligation
    ↓
continue machine
    ↓
reach correct stage
    ↓
perform effect
```

The second design makes time part of the program structure.

---

## Uncontrolled Time And Controlled Time

Many events occur in uncontrolled time.

```text
mouse click ──────────┐
network packet ───────┤
timer ────────────────┤
worker result ────────┼──► request state
filesystem event ─────┤
other subsystem ──────┘
```

The machine does not need to force those events to occur in a convenient order.

Instead, it converts them into state:

```text
pending work
pending mutation
pending refresh
pending persistence
pending message
```

The machine then processes those obligations in **controlled time**.

```text
uncontrolled arrival
        ↓
request becomes data
        ↓
machine-controlled stage
        ↓
effect
```

The queue, flag, or request record forms a boundary between these two temporal domains.

Early requests are not exceptional.

Late requests are not exceptional.

Out-of-order requests are not exceptional.

The system is designed to receive them whenever they occur and execute their consequences when the machine reaches the appropriate stage.

---

## A Machine Owns A Region Of Process

A module may implement one small machine.

That machine should usually have:

* a clearly identifiable job
* visible current state
* explicit entry points
* known process stages
* functions corresponding to machine operations
* clear boundaries where external input enters
* clear places where requests are recorded
* clear places where pending work is executed

For example:

```python
g = {
    "request": None,
    "source": None,
    "output": None,
    "radius-percent": None,
    "width": None,
    "height": None,
}
```

Then:

```python
def execute_round_corners():
    unpack_request()
    read_image_size()
    round_corners()
    write_output()
    set_success_outcome()
```

The important property is not that this uses globals.

The important property is that the source code exposes:

> **the process the machine is performing.**

---

## Machine State Is Not Hidden State

Machine state should be visible.

It should generally be:

* small
* named
* initialized
* inspectable
* governed by policy
* owned by a specific machine or subsystem
* changed at identifiable stages

For example:

```python
g = {
    "mode": "idle",
    "current-request": None,
    "save-requested": False,
    "state-stable": False,
}
```

This is not intended to be an unstructured pool of miscellaneous globals.

It is a **control panel**.

A programmer inspecting the machine should be able to answer:

```text
What is happening?
What is current?
What is pending?
What stage are we in?
What is allowed to happen next?
```

---

## Registers Represent Current Procedural Context

Registers are especially useful in machine coding because a process often has a current subject.

For example:

```python
reg["record"] = records[rid]

normalize_record()
validate_record()
update_indexes()
```

The register does not mean:

> this value belongs globally to the entire program.

It means:

> **this is what the current procedural flow is operating on.**

Likewise:

```python
reg["collision"] = collision
reg["panel"] = panel
reg["event"] = event
reg["node"] = node
```

Registers expose the working context of the machine.

They are analogous to working registers in physical or computational machinery: values that matter because of **where the process currently is**.

---

## Arguments Represent Real Choices

Machine coding tries not to confuse current machine state with caller choice.

Suppose the machine already has a current request.

Then:

```python
process_request()
```

may be more truthful than:

```python
process_request(request)
```

if callers are not actually intended to choose arbitrary requests.

Parameters should represent genuine degrees of freedom.

For example:

```python
select_record(rid)
move_item(src, dst)
set_value(key, value)
```

The arguments matter because the caller is choosing them.

But stable infrastructure or already-established current context should not be passed from function to function merely because each function needs access to it.

Doing so can create false generality.

---

## Exactness Over Fake Generality

Machine code should describe the actual mechanism that exists.

Do not make a concrete system pretend to be an abstract library merely because abstraction looks sophisticated.

Bad:

```python
def execute_round_corners(request):
    work = build_work_packet(request)

    read_image_size(work)
    round_corners(work)
    write_outcome(work)
```

if there is only one current round-corners job and all three functions operate on that same job.

Better:

```python
def execute_round_corners():
    unpack_request()
    read_image_size()
    round_corners()
    write_outcome()
```

The second form says:

> These are stages of one machine operating on its current state.

That is the truth of the program.

---

## The Difference From OOP

The distinction is not:

```text
classes bad
functions good
```

A machine could technically be implemented using a class.

An object-oriented program could technically be written without classes.

The distinction is the **primary organizing principle**.

Object-oriented design tends to organize software around:

```text
entities
ownership
encapsulation
behavior attached to data
messages between objects
```

Machine coding tends to organize software around:

```text
process
stages
system state
pending obligations
interactions
controlled transitions
execution order
```

Object-oriented thinking often asks:

> What does this thing do?

Machine thinking asks:

> What is the system doing with this thing?

Object-oriented thinking often asks:

> Which object owns this operation?

Machine thinking asks:

> Which process and stage own this operation?

Object-oriented thinking often makes behavior locally callable:

```python
obj.save()
```

Machine thinking may instead record:

```python
request_persistence()
```

and allow the persistence machine to execute later.

Object-oriented thinking tends to organize behavior **spatially**, around entities.

Machine coding tends to organize behavior **temporally and operationally**, around processes.

---

## Why The Difference Matters

The distinction matters because real systems often contain behavior that cannot be understood correctly in isolation.

Data participates in networks.

Operations affect multiple records.

Derived state depends on primary state.

Events arrive asynchronously.

Requests arrive before the conditions required to satisfy them.

Interactions involve multiple participants.

Some actions are valid only during specific phases.

If behavior is distributed into independently callable object methods, these temporal and relational constraints can become hidden.

The programmer must then reconstruct them dynamically:

```text
Is this safe now?
Has everything else finished?
Which other object needs updating?
Who owns this interaction?
What has already happened?
What still needs to happen?
```

Machine coding tries to put those answers into the architecture itself.

The machine says:

```text
This is the stage we are in.

These are the things currently being operated on.

These requests are pending.

These transformations happen now.

These invariants are established before we advance.
```

The process becomes visible.

---

## Core Principles

**The data does not do anything. The system does the data.**

**The central architectural question is: What process is this program performing?**

**Behavior belongs to processes and stages, not automatically to the nouns participating in them.**

**Requests and effects are different things.**

**Requests may arrive at any time. Effects should occur at the stage that owns the assumptions required to perform them correctly.**

**Prefer structural scheduling over arbitrary-time readiness checks.**

**Do not ask every caller to determine whether the universe happens to be safe right now.**

**Make the machine establish the conditions under which an operation occurs.**

**Interactions may belong to the system rather than to either participant.**

**Make time, state, pending work, and process boundaries visible in the code.**

---

## Questions To Ask When Designing A Machine

Ask:

```text
What process is actually happening?

What are its stages?

What state belongs to the process?

What is merely data being operated upon?

What is current?

What is pending?

What events can arrive at arbitrary times?

Which events should become queued requests rather than immediate effects?

At what stage should each pending operation actually execute?

What assumptions are guaranteed by reaching that stage?

What interactions exist between pieces of data?

Does an interaction really belong to either participant?

Am I attaching behavior to a noun merely because object-oriented grammar makes that feel natural?

Am I asking arbitrary code to prove that an operation is safe now when I could instead schedule it where safety is established structurally?
```

---

## Agent Instructions

When writing or refactoring Lion-style code:

* First identify **the process the program or module is performing**.
* Identify its important stages and ordering constraints.
* Distinguish request time from execution time.
* Turn arbitrary-time requests into explicit state, flags, events, or queued work when appropriate.
* Process those requests at the stage where their assumptions are valid.
* Prefer stages that establish correctness by construction over scattered `is_ready_now()` checks.
* Treat ordinary domain data as passive unless there is a strong reason to model it as an active actor.
* Do not automatically attach behavior to the data structure it concerns.
* Place interaction logic with the process that governs the interaction.
* Ask whether behavior belongs to A, B, or actually to a system operating on both.
* Make current machine state visible in small global bundles or appropriate registers.
* Use registers for current procedural context.
* Use arguments for real caller choices.
* Avoid passing current machine state as plumbing.
* Avoid fake generality.
* Prefer top-level procedures that visibly show the machine progressing through its stages.
* Make pending obligations visible.
* Make temporal boundaries visible.
* Make the code answer: **What is the machine doing now?**

---

## Signs The Machine Is Clear

The code is probably moving in the right direction when:

* the main procedure visibly describes the process
* important stages can be named
* operations occur at deliberate points in the process
* arbitrary-time events become explicit pending state when necessary
* request time and effect time are visibly distinct
* functions do not need widespread defensive readiness checks
* interaction logic is not artificially assigned to one participant
* data structures can remain ordinary data structures
* machine state is inspectable
* pending work is inspectable
* function signatures contain little plumbing
* the architecture makes ordering assumptions obvious
* the code feels like an operating system for the problem rather than a society of miniature objects

The goal is not to make everything abstract.

The goal is not to eliminate objects.

The goal is not to use globals for their own sake.

The goal is:

> **Make the operating process explicit, and make the program control when things happen.**
