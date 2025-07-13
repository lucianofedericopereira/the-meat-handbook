---
title: Signal Cheat Sheet
---

## Meat Signal Cheatsheet

| Concept                  | API or Pattern              | Notes                              |
|--------------------------|-----------------------------|-------------------------------------|
| Create a signal         | `meat()`                    | Base constructor                    |
| Read value              | `get(key)`                  | Sync read                           |
| Write value             | `set(key, value)`           | Sync update                         |
| React to changes        | `watch(key, fn)`            | Persistent callback                 |
| One-time reaction       | `once(key, fn)`             | Runs once then unbinds             |
| Batch mutations         | `merge({...})`              | Atomic multi-key updates           |
| Teardown listener       | `watch(...).stop()`         | Stop a live signal reaction        |
| DOM binding             | `linkToDOM(el, key)`        | Live data → attribute binding      |
| Plugin injection        | `meat.use(plugin)`          | Extend signal behavior             |
| Persist state           | `persist()` + `load()`      | Store and retrieve signal payload  |
| Safe commit             | `meat.safe(fn)`             | Rollback logic on failure          |
| Audit mutation          | `changedKeys()`             | Report changed keys                |
| Snapshot state          | `snapshot()`                | Read-only forked view              |
