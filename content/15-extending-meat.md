---
title: Chapter XII. Extending Meat
---

## Extending Meat

_Where augmentation respects runtime._

Meat’s extension model is simple by design—but that simplicity demands discipline.  Discipline here means plugins behave without surprises.

This chapter details how to extend the core safely, write reusable plugins, and clean up responsibly without ghosting the runtime.

### Adding Methods with Collision Safety

To extend `meat` without stepping on existing tools, use explicit checks:
```js
  if (!("logState" in meat)) {
    meat.logState = () => console.table(meat.getState());
  } else {
    console.warn("PLUGIN_CLASH: 'logState' already exists.");
  }
```

You can also run collision checks across API surface:
```js
  const before = Object.keys(meat);
  // plugin executes...
  const after = Object.keys(meat);

  after.forEach(k => {
    if (before.includes(k)) {
      console.warn(`PLUGIN_CLASH: Method "${k}" overwritten`);
    }
  });
```
<!-- Page Break --><div style="page-break-before: always;"></div>
Prefix methods to avoid namespace collisions:
```js
  meat.chronicle_undo = () => { ... };
```

### Reusable Utility Plugins

Shape plugins to be portable, scoped, and option-driven.
```js
  function counterPlugin(api, opts = {}) {
    let count = 0;
    const key = opts.watchKey ?? "count";
    api.watch(key, () => count++);
    api.getCount = () => count;
  }
```
Use `meat.use(counterPlugin, { watchKey: "clicks" })` for scoped behavior.

### Best practices:

- **Favor Options Over Globals**: Every plugin should accept an options object for configuration. Avoid relying on shared global state or side-effects. Scoped behavior ensures composability and lowers friction when debugging or refactoring.
- **Avoid Hard-Coded Keys**: Reactive keys should be declared via options.key or utility helpers—not buried in constants or assumptions. This prevents collisions and promotes isolation across extensions.
- **Respect `pluginAccess` Modes**: Each plugin receives an access scope: open, locked, or restricted. Never reach outside your sandbox. For example, avoid modifying external state unless granted elevated access.
- **Don’t Mutate Unless Required**: Prefer `get()` and observers over `set()` and side-effectual behavior. Mutations should be opt-in, explicitly tracked, and rollback-safe—especially in shared runtime environments.
- **Design for Reuse, Not Runtime Takeover**: Good plugins do one thing well. Don’t override core logic, duplicate state, or entangle yourself with global listeners. Instead, expose clean APIs and respect the host system’s boundaries.

### Plugin Cleanup Strategy

Every plugin should clean up:
```js
  function demoPlugin(api) {
    const timer = setInterval(() => console.log("tick"), 1000);
    demoPlugin.cleanup = () => {
      clearInterval(timer);
      console.log("Cleaned up demoPlugin");
    };
  }
```
Call `meat.unuse(demoPlugin)` and it triggers cleanup.

Use this for:
- Clearing intervals
- Removing DOM listeners
- Unsubscribing from `watch()` or `subscribe()`
- Tearing down resources

_No cleanup means potential leaks—especially in SPAs._

### Plugin Testing

Test like you ship:
- Attach plugin → observe effect
- Trigger mutations → confirm side effects
- Use `onAny()` to monitor emissions
- Detach with `unuse()` → verify teardown
- Mock flows via `meat.set()` or `meat.setState()`.
- Use `meat.devtools()` mid-test to inspect real-time state.
