---
title: Signal Patterns
---

## Signal Patterns

A quick mapping of MEAT’s core signal patterns to practical mental models:

| Signal Pattern     | Analogy            | Mental Model            |
|--------------------|--------------------|--------------------------|
| `watch(key, fn)`   | Security camera    | Passive observation     |
| `once(key, fn)`    | Tripwire trap      | One-shot reaction       |
| `set(key, value)`  | Manual override    | Direct mutation         |
| `merge({...})`     | Cargo manifest     | Batched updates         |
| `safe(fn)`         | Insurance policy   | Rollback protection     |
| `persist()` / `load()` | Flash drive     | Save & restore state    |
| `changedKeys()`    | Audit log          | Track modifications     |
| `snapshot()`       | Frozen scene       | Read-only fork          |

Use this table to orient thinking as you design reactive flows, plugins, and rollback strategies. Signals aren’t just syntax — they’re verbs with purpose.

