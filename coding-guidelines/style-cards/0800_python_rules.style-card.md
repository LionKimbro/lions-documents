```
title: Coding Style Card -- Python Rules
date: 2026-07-09
uuid: d4453132-1bef-4b19-a0ff-b6d3e1150d5b
taguri: lionkimbro@gmail.com,2026-07-09:style-card/python-rules/v0
```

# Lion Programming Guide: Python Rules

## Purpose

This is a seed card.

When this card is written properly, it should collect Python-specific
rules that support Lion-style procedural programs.


## Ideas To Include Later

* imports and module boundaries
* entrypoints and initialization
* avoiding import-time side effects
* when to use classes
* when not to use classes
* naming Python files and modules
* package layout for small tools
* test setup patterns


## No Loose Function Calls At Module Load

Avoid loose function calls floating around at module level.

Bad:

```python
register_builtin_jobs()
```

at the bottom of a module.

Module-level calls run at import time. This makes timing difficult to
control and can create surprising side effects when another module
imports the file for a different reason, such as testing,
introspection, CLI startup, or package discovery.

Prefer explicit initialization from a controlled entrypoint.

Good:

```python
def main():
    register_builtin_jobs()
    run_app()


if __name__ == "__main__":
    main()
```

If initialization grows larger, consider a small explicit `init()`
function or module that performs startup in a known order.


## Agent Instructions

When writing Lion-style Python:

* Do not leave operational function calls loose at module level.
* Keep import time quiet unless the module is only declaring constants,
  functions, data containers, or registrations that are intentionally
  passive.
* Put startup work inside an explicit `main()`, `init()`, or other
  controlled initialization function.

