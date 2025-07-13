---
title: Chapter III. State Management.
---

## State Management

_Mutation is engineered—not excused._

State isn’t sacred. And it isn’t chaotic. It’s just data—shaped, mutated, and observed through direct mechanisms. Meat gives you a clean set of primitives. No proxies. No lifecycle negotiations. No trick renderers. Just honest signal transmission and predictable state behavior that responds at the speed of your intent. This is mutation as infrastructure—not mysticism.

### Storing State

Under the hood, Meat maintains a plain object: No magic. No getters. No traps. Just key-value storage wired for visibility.

```js
let state = Object.create(null);
```
To read the full store:
```js
getState(); // Clones if immutable; returns reference if mutable
```

To inspect specific fields:
```js
  get("theme");       // → "dark"
  has("user");        // → true / false
```

Every access is direct. No wrappers. No memoization dance. If you know JavaScript, you know Meat’s surface.

### Mutating State

Single key updates happen via: `set("theme", "dark")`

Behind the scenes:
```js
state = config.mutable
? (state[key] = value, state)
: { ...state, [key]: value };
```

Every mutation triggers:
- A scoped signal: `meat:update:{key}`
- A full snapshot: `meat:update`

Batch updates use either `setState()` or `merge()`:
```js
setState({ user: "Luciano", locale: "it-IT" });
```
```js
merge({ online: true });
```
You choose mutation mode: 
- **Immutable** (`mutable: false`) – changes clone the object, preserving safety and traceability.
- **Mutable** (`mutable: true`) – mutations apply in-place, trading purity for speed.

Switch modes mid-flight with: _(Mutations are real-time, observable, and scoped to intent.
)_

- `freeze()`: `config.mutable = false`
- `thaw()`: `config.mutable = true`

### Reset and Wipe

To purge the entire store:

- `reset()`: clears all keys and emits global update
- `clear()`: same effect, used directly


```js
// Internally:
if (config.mutable) {
    Object.keys(state).forEach(k => delete state[k]);
  } else {
    state = {};
  }
```

Emissions include per-key `meat:update:{key}` with `undefined` and a global `meat:update`. Use it when you rebuild flows, clear form state, or reset reactive infrastructure.

### Tracking Mutations

Meat quietly tracks state changes for introspection and audit:
- `lastModified()`: timestamp (ms)
- `changedKeys()`:  array of keys mutated since last clear

These logs are updated every time a value shifts—whether by set(), setState(), or plugin-induced updates.This lets you power:
- Undo flows
- Dirty field markers
- Diff persistence
- Custom debugging overlays

It’s not a framework feature. It’s runtime fidelity.

### Serializing State

To represent your store as a JSON snapshot: `serialize()`. Returns a cached string unless the reference has shifted. You can use it for:

- `persist()` plugin (localStorage sync)
- `linkToDOM()` plugin (bind to DOM)
- Devtool tables
- Snapshot hashing or syncing

```js
// Internal logic:
if (prevRef === state) return cachedString;
  cachedString = JSON.stringify(state);
  prevRef = state;
  return cachedString;
```

No debounce. No middleware. Just memory-aware serialization.

### Inspection Tools

Sometimes you need to look inside—not just at the surface.

- `select(["user", "theme"])` → `sliced object`
- `hasChanged("theme", "light")` → `true / false`
- `isEmpty()` → `true if no keys`
- `inspectKey("user")` → `{ value, watched }`

These utilities help shape logic. You can check if a value changed before sending it. Or inspect whether a field is actively watched.

### Reactive Composition

Every mutation is accompanied by event emissions. You catch them via:

```js
  watch("user", val => {
    renderAvatar(val);
  });
  once("locale", val => {
    loadTranslation(val);
  });
  subscribe(snapshot => {
    updateSidebar(snapshot);
  });
```

All handlers fire synchronously after mutation. You never wait on a render cycle. You never chase stale data.

Want wildcards?
```js
  onAny((type, payload) => {
    logEvent(type, payload);
  });
```
Useful for logging, debugging, analytics, or reactive metrics.

Want to undo?
```js
  const stack = [];
  watch("content", val => stack.push(val));
  function undo() {
    if (stack.length > 1) {
      stack.pop(); // remove current
      set("content", stack.pop()); // restore previous
    }
  }
```

### Plugin Shaping
Plugins hook into mutations safely. For example, persistPlugin tracks all changes and lets you do:

- `persist("meatState")` stores serialized JSON
- `load("meatState")` reloads from localStorage

It uses the same mutation tracking: `trackMutation(key, value)`. Combined with `serialize()`, you control snapshot size, timing, and persistence scope.

Want to expose Meat globally for debugging?
- `bindToGlobal()` exposes meat to window
- `configurable()` logs current config flags
No ceremony. Just control.

### Usage Flow: Manual Control + Reactive Output

A typical composition might look like this:

```js
  setState({ user: "Luciano", theme: "dark" });
  watch("theme", t => {
    applyStyles(t);
  });
  persist(); // store snapshot
  serialize(); // get JSON
  console.table(getState()); // devtools
  if (hasChanged("theme", "light")) {
    set("theme", "light");
  }
  reset(); // full teardown
```

Each piece is inspectable. Each hook is manual. No render assumptions. No implicit subscriptions. Meat gives you signal—not scaffolding.

### Closing Pulse

Meat’s state engine is reactive, inspectable, and shaped with intent. No magic. No mystery. Just mutation that behaves, snapshots that serialize, and events that fire without lifecycle clutter. It’s engineered like infrastructure—meant to run, not to perform.

If the previous chapter gave you the blade’s composition, this one shows you how it slices in motion. Next we sharpen signal even further: scoped listeners, plugin coordination, and the full orbit of reactivity. Let’s keep building with grip.
