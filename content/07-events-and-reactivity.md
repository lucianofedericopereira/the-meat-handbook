---
title: Chapter IV Events & Reactivity
---

## Events & Reactivity

_Where signal moves without ceremony._

Meat’s event system is the backbone of its reactive architecture—a minimalist bus that connects mutations to listeners with no lifecycle rituals, no scheduler abstractions, and no framework interference. It’s built to behave in real time, from `set()` to `watch()`, from mutation to effect.

### Event Bus System

The event bus is powered by a Map. It wires event names to listeners, all invoked synchronously:
- `on(type, fn)` register a listener
- `off(type, fn)` remove listener
- `emit(type, data)` dispatch an event
- `onAny(fn)` global listener for all events
- `listeners()` inspect all active types
- `isWatched(key)` check listener presence

Each mutation in Meat triggers two events:
- `meat:update:{key}` scoped
- `meat:update` full snapshot

Listeners are always called synchronously, in insertion order. No deferrals. No surprises.

Use `onAny()` for logging and instrumentation:
```js
  onAny((type, payload) => {
    console.log("Event:", type, payload);
  });
```

### Scoped Reactivity

Meat supports fine-grained observation without lifecycle abstractions:
- `watch("theme", val => applyTheme(val))`
- `once("locale", val => loadLanguage(val))`
- `subscribe(snapshot => syncDashboard(snapshot))`

Each returns a teardown function:
- `const unwatch = watch("user", val => ...)`
- `unwatch()`  manual cleanup
- `once()` unbinds itself after firing once.  
- `watch()` observes a key.  
- `subscribe()` sees all updates.

Initial notification happens on registration. You never “miss” the first state unless you opt out.

Want to know if a key is actively watched? `isWatched("user")`

### Synchronous Behavior

All events fire in sync—post-mutation, pre-render. That means you can respond immediately:
```js
  set("ready", true);
  watch("ready", val => {
    if (val) activateView();
  });
```
The event system doesn’t wait. It’s designed to mirror runtime flow, not wrap it. This directness keeps logic traceable. No zone.js, no reactivity trees, no “why didn’t this trigger yet?”

### Debugging the Flow

Enable `config.debug` to trace internal emissions and behaviors:
```js
  config.debug = true;
  debugLog("listener-registered", { key, fn });
```
Warnings emit via: `warn("HANDLER_ERROR", error)`

Devtools print state clearly: `devtools()` logs state as table

You can inspect active channels:
- `listeners()` → `[ "meat:update:user", ... ]`
- `isWatched("theme")` → `true` / `false`

Pair `onAny()` with external monitors for reactive analytics.

### Audit & Intent

Combine with mutation tracking for full visibility:
- `changedKeys()` → [ "`theme`", "`user`" ]
- `lastModified()`→ `timestamp`

Every listener is part of a precise chain. You know what changed. You know which signals fired. You know what responded. No lifecycle guessing. No async drift.

### React Without Framework

Reactivity in Meat isn’t tied to renderers, component trees, or lifecycle abstractions. It’s reactive in the original sense: you mutate, it responds. That’s it. You don’t import hooks. You don’t orchestrate hydration. You wire behavior directly.

- `const show = el => el.style.display = "block"`
- `const hide = el => el.style.display = "none"`
```js
// Use case: DOM toggle
watch("visible", v => {
    v ? show(panel) : hide(panel);
  });
```
No virtual DOM. No re-render diffing. Just intent flowing from state to output.

```js
// Use case: network sync
watch("query", value => {
  fetch(`/search?q=${value}`)
    .then(res => res.json())
    .then(data => set("results", data));
});
```

Reactive without framework. Declarative without syntax tricks. You mutate query, Meat reacts, fetch runs.

```js
// Use case: canvas redraw
watch("angle", deg => {
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  drawArrow(ctx, deg);
});
```

Mutation drives signal. Signal drives redraw. It’s tight, observable, and purpose-built.

```js
// Use case: global listener
onAny((type, payload) => {
  analytics.track(type, payload);
});
```

You log every mutation, every flow, every scoped change—without ceremony. Meat doesn’t care if your app is HTML, SVG, WebGL, JSON over wire, or serverless state sync. It emits. You respond.
```js
// Listener cleanup? Manual when you want it:
const unwatch = watch("theme", applyTheme);
unwatch(); // tear down
```
No dependency arrays. No unmount guessing. You control the link—and its teardown. Reactivity here is signal first. It’s not react-like. It’s reaction—without abstraction. You observe. You mutate. You respond. Every listener runs in sync. Every flow stays traceable. This isn’t a framework—it’s **a reactive layer you can reason about**.

### Closing Signal

Events in Meat behave the way developers expect: predictably, synchronously, observably. No hook fatigue. No framework lock-in. This is reactivity built on clarity—not context. If Chapter III showed how mutation behaves, this chapter shows how signal responds. Meat moves when your code moves. And nothing stands in the way of your intent.
