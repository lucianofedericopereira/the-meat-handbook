---
title: Chapter VI. Built-in Plugins
---

## Built-in Plugins

_Where extension meets utility._

Meat ships with built-in plugins designed for real-world utility—not theory. These plugins operate without introducing hierarchy, rendering dependencies, or lifecycle complexity. Each one extends runtime behavior from a principled, scoped point of view.

### **Persist Plugin**: LocalStorage-powered snapshot saving.
- `persist(key)` save serialized state to disk
- `load(key)` restore from disk
- `freeze()` force immutable mode
- `thaw()` allow mutable mode
- `lastModified()` timestamp of last change
- `changedKeys()` diff since last clear
- `bindToGlobal(name)` exposes meat globally
- `configurable()` print config flags
Used for offline sync, hydration flows, and minimal durability features—without reaching for IndexedDB or state frameworks.

### **Link Plugin**: DOM binding without render engine.
- `linkToDOM(selector, attr)` sync state to DOM attribute
- `unbindDOM()` teardown binding

Useful for dashboards, inspection overlays, status bubbles, or pure-data UIs. No virtual DOM assumptions. Just state → markup.

### **LogState Plugin** Console visibility, single method.
- `logState()` console.table of current state

Drop it when you want fast devtools signal without overhead. Especially useful in immutable debug sessions or plugin sandbox inspection.

### **Plugin logic**: Simple. Visual. No config required.
```js
  meat.logState = () => {
    console.table(meat.getState());
  };
```

### **MeatChronicle Plugin** 

_Time-series, undo, rollback, audit tooling_

#### What It Does

- Tracks every mutation across keys in a bounded history map
- Allows per-key `undo()` restoration
- Supports global `rollbackAll()`
- Wraps side-effect blocks with `safe(fn)` for auto-rollback on error
- Surfaces change logs via `getHistory()`, `logHistory()`, and `historySnapshot()`
- Offers `changedKeys()` to inspect what moved and when, based on the keys touched since the last full reset or snapshot. This lets you analyze field-level activity and trace application behavior over time.

Each key gets a chronological log: `{ value, timestamp, source }`. The source field marks context: "mutation", "undo", "rollback", etc.

#### Installation: 

`meat.use(MeatChronicle, { limit: 100 })`: The "limit" defines how many snapshots per key are retained.   Once full, older entries are removed as new ones come in. Useful for:
- Undo/redo in forms
- Async safety nets
- Audit trails
- Telemetry and diagnostics

#### Real-World Usage
- Undo a key’s last change: `meat.undo("theme")`
- Rollback all fields: `meat.rollbackAll()`
Protect side effects:
```js
  meat.safe(() => {
    meat.set("count", null);
    throw new Error("Boom");
  });
```
The plugin auto-restores previous state and logs the failure.
Inspect timeline: `meat.logHistory("theme")`
Access raw entries: `meat.getHistory("theme")`
Dump all logs: `meat.historySnapshot()`
Check touched keys: `meat.changedKeys()`

#### Setup Flow

When installed, the plugin watches every mutation:
```js
  meat.subscribe(snapshot => {
    Object.entries(snapshot).forEach(([key, value]) => {
      push(key, value, "setState");
    });
  });
```

Each mutation is pushed to its key’s log. If limit exceeded, old entries are dropped.

Rollback logic is simple and scoped:

- `meat.undo(key)` revert one
- `meat.rollbackAll()` revert everything
- `meat.safe(fn)` run with protection

You can also inspect the mutation source and timestamp on every log entry for analytics or diffing.

## Framework Plugins

You can build plugins that wire into UI libraries without binding to their internal lifecycles.

Framework plugins don’t mutate render engines—they pass signal into them. This keeps reactive behavior decoupled from UI logic. Mutations stay in Meat. Listeners forward state into view.

Meat provides a set of integration plugins that connect its core runtime to popular UI frameworks—without imposing lifecycle, hydration, or reactivity models. 

These plugins expose Meat’s signal system to the host environment while preserving the principle of external observability.

The plugins don’t reimplement Meat’s API. They expose it, inject it, or forward it. Behavior stays untouched. Signal stays pure.

### Alpine
`meatAlpinePlugin()`:
- Registers MEAT into Alpine’s reactive evaluator.
- Updates state via evaluateLater + `effect()`.
- Ideal for embedding Meat in declarative markup.

### Angular
`MeatService` (Injectable)
- Provides `get()`, `set()`, `watch()`, `subscribe()` from MEAT core.
- Injectable into Angular components.
- Integrates signal without NG lifecycle coupling.

### Astro
`meatAstroPlugin()`
- Adds MEAT state as a serialized payload to window via injectScript.
- Enables hydration-aware use or inspection from client-side scripts.

### Next
`meatNextPlugin(app)`
- Attaches MEAT to the app object.
- Minimal wrapper for global access in app components.

### Nuxt
`defineNuxtPlugin()`
- Provides MEAT via nuxtApp context.
- Exposes state engine to Vue/Nuxt composition APIs.

### Qwik
`meatQwikPlugin()`
- Adds MEAT to Qwik’s provide system.
- Enables reactive signal bridging across islands or loaders.

### React
`useMeat(key)`
- Custom hook that subscribes to MEAT and re-renders component.
- Provides runtime-safe access to reactive keys.

### Solid
`meatSolidPlugin()`
- Returns MEAT through Solid’s provide pattern.
- Allows components to access and mutate state directly.

### Svelte
`meatSveltePlugin(app)`
- Injects MEAT into the Svelte app context.
- Enables direct subscriptions and debug toggling.

### Vue
`meatVuePlugin`
- Installs MEAT as $meat on globalProperties.
- Available in all components via this.$meat or Composition API.

:::note[Plugins serve one job]
Make Meat available without controlling behavior. Each plugin is separate and scoped.No wrappers. No abstraction chains. Just exposed runtime.This is integration without compromise—and signal you control.
:::

### useMeat() 

Before we close the chapter on framework plugins, it's worth noting a common utility pattern found across integration folders: `useMeat`.

While the core MEAT runtime doesn't depend on any framework, many plugins ship helper utilities named `useMeat.js` or `useMeat.ts`. These act as reactive bindings, usually wrapping MEAT's `subscribe()` logic into framework-native reactivity.

For example:

Angular `useMeat(key: string)`
- Wraps state access with RxJS BehaviorSubject
- Provides value asObservable() for Angular templates and services
```js
    export function useMeat(key: string) {
      const subject = new BehaviorSubject(meat.get(key));
      meat.subscribe(key, value => subject.next(value));
      return subject.asObservable();
    }
```

React `useMeat(key: string)`
- Uses useState() and useEffect()
- Subscribes to MEAT, triggers local re-render
```js
    export function useMeat(key) {
      const [value, setValue] = useState(meat.get(key));
      useEffect(() => {
        const unsub = meat.subscribe(key, setValue);
        return unsub;
      }, [key]);
      return value;
    }
```

Svelte, Solid, Vue, Qwik, and Nuxt plugins may ship similar useMeat files—but not all are populated. 

**Important**: these utilities are optional.  
You don't need them to access MEAT inside a framework.   They just smooth the reactive edge if you want plug-and-play syntax.

