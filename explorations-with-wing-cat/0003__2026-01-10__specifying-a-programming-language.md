date: 2025-01-10
development: https://chatgpt.com/c/696336ae-6874-832a-975b-c152bbc182b1


# 📜 A.D.H.D. Core Constitution v0 -- "A Developers Hyper-Drive" (A.D.H.D.)

*A Visual, Permissioned, Polyglot Programming Medium*

*(Draft — January 2026)*

---

## 0. Guiding Principles

A.D.H.D. is not merely a textual programming language.
It is a **visual programming medium** hosting multiple execution dialects under a shared runtime model.

Its primary goals are:

1. **Traceability** — relationships between components must be visible and enforceable.
2. **Promise Enforcement** — architectural intent must be mechanically checked, not merely documented.
3. **Power with Consent** — dangerous capabilities are allowed, but only by explicit grant.
4. **Human-Scale Causality** — control flow and data movement must be intelligible to the human mind.
5. **Polyglot by Design** — multiple execution dialects (e.g., Python-like, C) coexist within one system.

---

## 1. Entities and Identity

### 1.1 Entity Types

All meaningful program elements are **entities**:

* Variable (data slot)
* Function
* Thread
* Queue
* Memory Region
* Lens (naming convention)
* Native Function (C)
* Code Region (executable memory)

Each entity has:

* **Stable Identity** (ID, not derived from name)
* **Kind**
* **Metadata Map** (arbitrary user/tool-defined fields)
* **Tags** (symbols used for permission logic)

### 1.2 Identity vs Name

Names are **aliases**, not identities.

* Names are bound to entity IDs in a **binding environment**.
* Bindings are contextual and hierarchical.
* Multiple names may refer to the same entity.
* The same name may refer to different entities in different regions.

Thus:

> Names exist for humans; identity exists for the system.

This enables:

* domain-specific naming (e.g., `F`, `m`, `a`)
* discipline-specific vocabularies
* visual grouping to imply binding scope

---

## 2. Symbols

Symbols are identifiers that evaluate to their own textual name.

By convention:

* Written in ALL_CAPS
* Semantically equivalent to `"SYMBOL_NAME"`

Symbols are used for:

* tags
* permissions
* registry (dictionary) keys
* metadata references

Symbol use prevents silent typos and supports tooling-level introspection.

---

## 3. Functions and Globals

### 3.1 Functions

Functions are sequential, single-threaded execution units.

Each function has:

* parameters
* local variables
* body
* tags
* call permissions

Functions may declare:

* **How many may call them**

  * ex: "only 1 other function may call this function"
  * ex: "up to 3 other functions may call this function"
  * ex: "any number of functions may call this function"
  * ex: "at least 2 functions must call into this function"

* **Who may call them**

  * specific functions
  * functions with certain tags

Violations are:

* compile-time errors when statically resolvable
* runtime errors otherwise

### 3.2 Globals (Variables)

Variables are **data slots**, not names.

Globals are normal and encouraged.

Each variable may declare:

* read permissions
* write permissions
* optional type constraints

Typing is:

* optional
* enforced at runtime or compile-time when available

Variable identity is stable across threads and contexts, subject to thread policy (see §5).

---

## 4. Object Lenses (Virtual Interfaces)

A.D.H.D. has no classes.

Instead, it supports **lenses**:

A lens is a mapping:

```
method_name → function_entity
```

Within a binding environment, the lens enables:

```
x.foo(y)
```

to rewrite as:

```
foo_impl(x, y)
```

Lenses are:

* statically bound in environments
* not dynamically dispatched
* naming conventions, not ownership models

This allows:

* polymorphic calling style
* without object method tables or inheritance

---

## 5. Threads and Queues (Concurrency Model)

Concurrency exists but is explicitly structured.

### 5.1 Threads

Threads are long-lived execution contexts.

Each thread has:

* tags
* owned memory regions
* inbound queues

Threads may be:

* producers
* consumers
* native execution hosts

### 5.2 Queue-Based Communication

All cross-thread interaction occurs via **queues**.

Queues are entities with:

* target thread
* read permissions (by tag or identity)
* write permissions (by tag or identity)

Queues may carry:

* GC-managed objects (by transfer or copy)
* memory region handles
* scalar data

This forms the foundation of async behavior.

There is no implicit stack suspension or hidden continuation model.

---

## 6. Memory Model

### 6.1 Memory Regions

All raw memory lives in explicit **regions**.

Region kinds:

1. **Thread-Owned**

   * accessible only by owner thread

2. **Thread-Local**

   * separate instance per thread

3. **Shared Byte Regions**

   * byte-addressable
   * access controlled by permissions

GC-managed objects **never** live in shared regions.

### 6.2 Allocation Models

Regions may be:

* Static (allocated at program start)
* Arena-based (bulk allocation + bulk release)

Views into regions provide:

* offset + length
* typed interpretations

---

## 7. Native Power: C as a Sub-Language

### 7.1 C as Embedded Dialect

A.D.H.D. allows C code blocks directly within the program.

C declarations use standard C function signatures:

```c
int fast_fn(int x, const char* src, char* dst) { ... }
```

These define **native function entities**.

Two modes:

* Inline C (compiled from source)
* Extern C (linked to external library symbols)

### 7.2 Toolchain Integration

C code is compiled via:

* LLVM/clang or compatible backend
* automatically managed by the environment
* cached and incrementally rebuilt

Native functions participate fully in:

* permission system
* tagging
* thread affinity rules

### 7.3 Thread Safety Declarations

Native functions must declare:

* `@thread_affine(tag)`
* `@thread_safe`
* or `@serialized(queue)`

This prevents unsafe concurrent invocation.

---

## 8. Executable Memory (POKE Model)

Executable memory is supported but capability-gated.

### 8.1 Code Regions

Threads may own **code regions**:

* initially writable, not executable
* finalized to executable, not writable (W^X)

Primitive operations:

* `poke(offset, byte)`
* `emit(bytes)`
* `seal_exec()`

Finalization produces callable code handles.

### 8.2 Permissions

Executable memory use requires:

* global compile-time enable flag
* runtime capability tags
* explicit thread ownership

Environments may fully disable executable memory.

---

## 9. Visual Medium Mapping

The editor is not decoration; it is part of the language.

### 9.1 Spatiality

* 2D placement conveys grouping and architecture
* scale conveys semantic weight

### 9.2 Shapes

* Rectangles: entities
* Lines: relationships
* Arrows: directionality (calls, queues)

### 9.3 Icons

* 16×16 user-authored symbols
* embedded inline with text
* stored in Unicode private-use ranges

Used for:

* entity roles
* permissions
* heraldry

### 9.4 Color Channels

Exactly four semantic channels:

1. Primary — core structure
2. Secondary — supporting structure
3. Annotation — commentary and tooling
4. Hot — execution and alerts

Color is syntactic, not stylistic.

---

## 10. Async as Structured Time

A.D.H.D. does not use implicit async/await semantics.

Temporal decoupling occurs via:

* thread boundaries
* queues

Higher-level async abstractions may exist, but must compile to:

> message send + later message receipt

This preserves traceability and causality.

---

## Closing Statement

A.D.H.D. is not optimized for:

* minimal keystrokes
* maximal compiler cleverness
* abstract purity

It is optimized for:

* architectural clarity
* power with responsibility
* systems that can be **seen, understood, and trusted**

It is a language for people who build worlds, not just algorithms.

