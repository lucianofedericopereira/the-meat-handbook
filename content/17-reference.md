---
title: Chapter XIV. Complete API Reference. 
---

## Public Interface

This chapter gives you a full overview of Meat’s public interface, plugin behavior, debug utilities, and glossary terms. From top-level state control to plugin lifecycle, everything is here. No scaffolding. No hidden methods. Just the full surface.

### State & Mutation API

| State & Mutation API       | Description                                               |
|----------------------------|-----------------------------------------------------------|
| `getState()`               | Returns the full reactive state object                    |
| `get(key)`                 | Retrieves the value for a specific key                    |
| `set(key, value)`          | Updates a key and triggers a mutation                     |
| `setState(obj)`            | Sets multiple keys using a batch update                   |
| `merge(obj)`               | Alias for `setState()` with shallow merge semantics       |
| `clear()` / `reset()`      | Removes all keys from the reactive store                  |
| `serialize()`              | Provides a cached JSON string of current state            |
| `select([keys])`           | Returns a shallow clone of selected state keys            |
| `has(key)`                 | Checks if a key exists in the current state               |
| `hasChanged(key, value)`   | Compares value against current key for mutation check     |
| `isEmpty()`                | Returns `true` if state has no keys                       |
| `changedKeys()`            | Lists keys that were modified since last snapshot         |
| `lastModified()`           | Returns timestamp of the most recent mutation             |

### Event System API

| Event System API          | Description                                           |
|---------------------------|-------------------------------------------------------|
| `on(type, fn)`            | Registers a handler for the given event type          |
| `off(type, fn?)`          | Removes a specific handler or all handlers for a type |
| `emit(type, payload)`     | Triggers a custom event with optional payload         |
| `onAny(fn)`               | Registers a wildcard listener for all event types     |
| `watch(key, fn)`          | Subscribes to changes on a specific key               |
| `once(key, fn)`           | Registers a one-time listener for key mutation        |
| `subscribe(fn)`           | Observes global snapshot updates                      |
| `unbindAll()`             | Clears all listeners and subscriptions                |
| `listeners()`             | Returns an array of active event types                |
| `has(type)`               | Checks if any listener exists for a given type        |
| `isWatched(key)`          | Returns true if key has an active watcher             |

### Plugin Lifecycle

| Plugin Lifecycle Method     | Description                                               |
|-----------------------------|-----------------------------------------------------------|
| `use(pluginFn, options?)`   | Registers a plugin with optional configuration            |
| `unuse(pluginFn)`           | Unregisters a plugin and triggers its cleanup callback    |
| `pluginAccess`              | Determines scope of access: `open`, `locked`, `restricted`|
| `createPluginAPI()`         | Generates an internal scoped API for safe plugin logic    |

Plugins never override core logic. They only extend or observe. All mutations tracked.


### Built-In Plugins

| Plugin                      | Purpose                                              |
|-----------------------------|------------------------------------------------------|
| `persist(key?)`             | Saves current state snapshot to `localStorage`       |
| `load(key?)`                | Restores or hydrates state from `localStorage`       |
| `freeze()` / `thaw()`       | Enables or disables immutable state mode             |
| `dump()`                    | Returns a cloned snapshot of the current state       |
| `linkToDOM(selector, attr)` | Binds serialized state to a DOM attribute            |
| `unbindDOM()`               | Removes DOM listener created by `linkToDOM()`        |
| `configurable()`            | Exposes runtime configuration flags and settings     |
| `devtools()`                | Displays current state using `console.table()`       |
| `inspectKey(key)`           | Returns the current value and watcher status         |

### MeatChronicle Plugin _(if enabled)_

| Method                | Description                                     |
|-----------------------|-------------------------------------------------|
| `undo(key)`           | Revert key to its previous value                |
| `rollback(key)`       | Alias for `undo(key)`                           |
| `rollbackAll()`       | Revert all tracked keys to their prior states   |
| `safe(fn)`            | Execute block with rollback on error            |
| `getHistory(key)`     | Retrieve timeline of all mutations for a key    |
| `clearHistory(key?)`  | Clear mutation history (for a key or all)       |
| `logHistory(key)`     | Display mutation history as a console.table     |
| `historySnapshot()`   | Clone full mutation history for inspection      |
| `logMessage(msg, ctx)`| Log a timestamped debug message with context    |

### logState Plugin _(if enabled)_

| Method                     | Description                                          |
|----------------------------|------------------------------------------------------|
| `logState()`               | Displays the current reactive state as `console.table()` |

### Config

| Config Key               | Description                                      |
|--------------------------|--------------------------------------------------|
| config.debug             | Enable debug output                              |
| pluginAccess             | Defines scope for plugin APIs                    |
| Immutable Mode           | Controlled by freeze/thaw                        |
| Safe Mode                | Enabled per plugin via access flag               |
| Chronicle Retention      | Set via plugin options { limit }                 | 

### Glossary of Terms

| Term                     | Definition                                       |
|--------------------------|--------------------------------------------------|
| State                    | Core reactive store                              |   
| Mutation                 | Any state change                                 | 
| Immutable                | Copy-based state behavior                        |
| Listener                 | Function hooked into signal                      |
| Event Bus                | Internal reactive pub/sub                        |
| Plugin                   | External extension to core logic                 |
| Snapshot                 | Serialized or frozen state                       |
| Rollback                 | Reversion of key to prior value                  |
| Sync                     | Client-server state transmission                 |
| Wildcard Listener        | Listener invoked for all events                  |
| Plugin Clash             | Method overwrite warning                         |

<!-- spacer --><div style="height: 8px;"></div>

### Common Warnings

| Warning Code             | Description                                      |
|--------------------------|--------------------------------------------------|
| PLUGIN_CLASH             | Plugin tried to overwrite core method            |
| PERSIST_FAIL             | localStorage write failure (e.g. quota, blocked) |
| HANDLER_ERROR            | Error occurred inside listener callback          |
| WILDCARD_ERROR           | Exception thrown from wildcard onAny listener    |
| CONFLICTING_ACCESS_MODE  | Plugin attempted mutation in locked mode         |
| SAFE_EXEC_ERROR          | Error caught during safe() execution; rollback   |

### Recent Additions _(validated and included)_

- `setState()` and `merge()` aliases confirmed.
- `select()` for partial access.
- `hasChanged()` for comparison logic.
- `dump()` added for raw snapshot.
- `inspectKey(key)` shows watchers and value.
- `safe(fn)` for transaction rollback.
- `logState()` and `devtools()` for console visibility.
- `Chronicle` audit features fully included.
- `pluginAccess` config confirmed per plugin.
- `linkToDOM` and `unbindDOM` verified.
- `wildcard` and `once` listeners included.
- `createPluginAPI()` internal exposed for reference.

Everything surfaced. Nothing omitted. This reference block reflects every known public method, plugin interface, warning, and glossary item inside Meat’s runtime as of current specification.
