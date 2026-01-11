date: 2027-01-07
origin: https://chatgpt.com/c/695e0070-8d74-832d-aded-86522bede98d

# Why Fewer Parameters Often Means Better Architecture

*(or: Why My Best Functions Take Zero Arguments)*

For years, I had a persistent, annoying instinct when writing software: I hated functions with long parameter lists. Not in a vague stylistic way, but in a deep, architectural way. If a function needed five or six arguments, I felt like something was wrong with the design — even if the code “worked.”

This instinct is often dismissed as personal taste. After all, explicit parameters are supposed to be good, right? Isn’t passing data around cleaner than using shared state? Isn’t functional purity the gold standard?

And yet, in real interactive systems — especially GUIs — long parameter lists are frequently a symptom of a deeper structural problem: **the system doesn’t yet know where truth lives.**

Let’s talk about why.

---

## When Functions Carry the World on Their Backs

In many programs, especially event-driven ones, you’ll see handlers like this:

```python
def on_mouse_move(x, y, selected_id, hovered_id, drag_mode, camera_x, camera_y):
    ...
```

This function is being asked to do two things at once:

1. Reconstruct the situation of the world
2. Decide what to do in that situation

Every call site must gather all the facts needed to describe reality and then pass them in. That means **the meaning of the system is scattered across call graphs instead of living in one place.**

As the system grows, every new feature adds another parameter. Soon, every function needs to know everything.

That’s not modularity. That’s manual state threading.

---

## Centralizing Truth Changes Everything

Now consider a different approach:

* There is a single model of the world.
* There is a single place where perception happens.
* There is a single place where coordinate transforms happen.
* There is a single place where rendering happens.

When perception and state are centralized, most functions stop needing data as parameters. They can simply consult the current state.

Instead of:

```python
start_drag(x, y, selected_id, top_item, drag_mode)
```

You get:

```python
start_drag("item")
```

And that’s not laziness. That’s clarity.

The system already knows:

* where the mouse is
* what’s under it
* what item is selected
* what coordinate space it’s in

So the function only needs to express **intent**, not reconstruct context.

---

## Verbs vs Plumbing

This is the key shift:

When functions take many parameters, they are doing **plumbing**.
When functions take few or none, they are doing **verbs**.

Plumbing functions say:

> Here is how to assemble reality so I can operate.

Verb functions say:

> Given reality, do this.

And that difference is enormous for how code feels, reads, and evolves.

Consider:

```python
move_item(item_id, dx, dy, camera_x, camera_y, zoom)
```

versus:

```python
slide_rect(dx, dy)
```

In the second case, the machine already knows which rectangle is active, what coordinate system it’s in, and how to interpret the movement. The function expresses only the operation.

That’s not hiding information — that’s placing information where it belongs.

---

## Registers, Not Arguments

Architectures that feel “simple” at the surface often rely on internal machinery:

* registers
* state dictionaries
* pipelines
* rule engines
* render passes

These systems trade parameter passing for **stateful stages of transformation**.

Instead of:

```
input → function(arguments) → output
```

you get:

```
input → load registers → transform registers → store registers
```

This is how CPUs work.
It’s how graphics pipelines work.
It’s how physics engines work.

And it’s incredibly powerful for UI systems, because interactions are not isolated calls — they are **continuous processes** acting on evolving state.

---

## Why GUIs Especially Benefit from This

GUIs have several unavoidable realities:

* Events are frequent and noisy.
* Coordinates must be transformed constantly.
* Hit-testing, snapping, dragging, and selection are interdependent.
* Rendering must reflect state continuously, not just in response to calls.

Trying to pass all of that through parameters leads to:

* duplicated calculations
* inconsistent interpretations
* fragile event handlers

Centralizing perception (what happened), state (what is), and transformation (what changes) allows each handler to focus on one thing: **decision making.**

This is why frameworks often have:

* models
* controllers
* render loops
* event dispatch systems

Even when programmers don’t consciously build those layers, they tend to rediscover them out of necessity.

---

## Fewer Parameters Forces Better Responsibility Boundaries

When you constrain yourself to 0–2 parameters, something interesting happens:

You are forced to ask:

* Where should this information live?
* Who is responsible for computing this?
* Why does this function need to know this at all?

It becomes very hard to smuggle architectural confusion through a tiny interface.

Long parameter lists are often not clarity — they are **unresolved design questions leaking through function signatures.**

Short signatures demand answers.

---

## This Is Not Anti-Functional Programming

It’s important to say this: this is not an argument against functional programming, immutability, or explicit data flow in general.

This is an argument against **forcing event-driven systems into stateless call models** when the domain itself is inherently stateful.

A GUI is not a batch computation.
It is a continuously evolving simulation.

Trying to pretend otherwise usually makes the code harder, not cleaner.

---

## Debugging Becomes Easier, Not Harder

A common fear is that centralized state makes debugging worse.

In practice, the opposite often happens.

Instead of tracing 12 arguments across 5 call layers, you can inspect:

```python
CUR
G_ATTACH
G_CANVAS
G_DRAG
```

and immediately see what the system believes about:

* geometry
* selection
* interaction
* rendering intent

Bugs become mismatches between **model and behavior**, not mysteries hidden in parameter plumbing.

---

## Why This Feels Like “Elegance”

What programmers often call “elegant code” is not minimal syntax — it is **conceptual alignment**.

When:

* perception is centralized
* state is authoritative
* transformations are mechanical
* actions express intent

then functions naturally shrink.

Not because you tried to make them short, but because there is less confusion for them to compensate for.

That’s why the feeling is so strong when a handler becomes:

```python
start_drag("item" if CUR["top_item_id"] else "pan")
```

It reads like thought, not machinery.

---

## Conclusion: Fewer Parameters Is a Signal, Not a Rule

This isn’t about obeying a stylistic guideline.

It’s about recognizing a pattern:

> When a system is well factored, functions stop needing to carry the world with them.

They can assume a world exists, and operate within it.

And when that happens, code becomes:

* easier to extend
* easier to reason about
* easier to debug
* and frankly, more joyful to write

Which, in the end, is what we were chasing all along.



