---
title: Chapter VII. Debugging &  Dev Tools
---

## Debugging &  Dev Tools

_Where inspection meets truth._

Meat is engineered for runtime clarity. It doesn’t abstract mutation—it exposes it. This chapter walks through the debugging surface, devtools integrations, introspection utilities, and runtime diagnostics you can use during development, audit, and reactive reasoning.

### Core Debug Controls

- `meat.config.debug = true` Set a global flag to enable internal debug output: This enables console tracing for plugin setup, listener registration, mutation events, and conflict warnings.

Trace warnings manually via:
```js
meat.warn("PLUGIN_CLASH", { key: "set" });
```

Use `debugLog()` to emit structured messages:
```js
  debugLog("watch-attached", { key, fn });
```

### State Inspection

You can inspect the current store with:

```console.table(meat.getState());         // full state
  console.log(meat.select(["theme"]));    // partial state
  console.log(meat.has("user"));          // key check
  console.log(meat.inspectKey("locale")); // value and watched status
```

Use `logState()` plugin to simplify: `meat.logState()`

### Event Visibility

Check which listeners are active:
- `meat.listeners()` → array of active event types
- `meat.isWatched("theme")` → true / false

Wildcard listeners can mirror traffic:
```js
  meat.onAny((type, payload) => {
    console.log("Event:", type, payload);
  });
```
Useful for live logging, testing, and reactive diagnostics.

### Mutation Tracking

Audit how state moves over time:
```js
  meat.changedKeys();      // → array of touched keys
  meat.lastModified();     // → timestamp of last change
  meat.serialize();        // → JSON snapshot
```
This helps you debug:
- Dirty fields in forms
- Reactive flows in UI
- State-based navigation changes

### Devtools Access

Use `meat.devtools()` to print state as a table:
`meat.devtools()` → console.table of store

Can be used at breakpoints, or inserted into runtime flows for visibility.

Pair with:
```js
  console.group("Mutation Audit");
  meat.devtools();
  console.groupEnd();
```

### Plugin Diagnostics

Plugins can introspect mode, config, and exposed API surface:
```js
  if (meat.config.pluginAccess === "locked") { ... }
```

They can also emit safe logs:
```js
  meat.logMessage("Plugin initialized");
```

Pair with `MeatChronicle` for mutation journaling and rollback auditing.

### Live Debug Scenarios

Example: verifying listener execution
```js
  meat.watch("ready", val => console.log("READY:", val));

  meat.set("ready", true);
```

Expect immediate output, no delay. Listeners are synchronous. Nothing hidden. Example: confirm plugin conflict
```js
  meat.set = () => { /* overridden */ };
  meat.use(conflictPlugin); // warns on method clash
```
