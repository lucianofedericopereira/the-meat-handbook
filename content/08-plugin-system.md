---
title: Chapter V Plugin system
---

## Plugin System

_Extensibility without compromise._

Meat supports live augmentation through a plugin model built on simplicity, safety, and clarity. Plugins behave like runtime extensions—but they don’t hijack internals. They inherit only what you expose. You control the API surface. They operate in isolation.

This isn’t a framework-style plugin system. There’s no registry. No lifecycle tax. Just functions with scoped access, wired to behave like tools—not authorities.

### Philosophy & Control Scopes

Plugins don’t get access to everything by default. That’s intentional.

At install, you select an access mode via config:
```js
  config.pluginAccess = "restricted"; // or "open", "locked"
```
Each mode shapes what the plugin receives:
- `open` — Full access to Meat API. Suitable for trusted internal use.
- `restricted` — Only the methods explicitly whitelisted in config.
- `locked` — Read-only access: `getState()`, `serialize()`, config copy.

This ensures third-party or shared plugins operate within bounds—no surprise mutations, no hidden subscriptions.

<!-- Page Break --><div style="page-break-before: always;"></div>
Internally, each plugin receives:
```js
  function createPluginAPI(mode, whitelist) {
    if (mode === "open") return meat;
    if (mode === "restricted") return pick(meat, whitelist);
    if (mode === "locked") return {
      getState,
      serialize,
      config: clone(config)
    };
  }
```

### Lifecycle

Registering is direct:
```js
  meat.use(pluginFn, options);
```

Unregistering is surgical:
```js
  meat.unuse(pluginFn); // calls pluginFn.cleanup() if present
```
Each plugin is a function that receives: `pluginFn(api, options)`

Side effects are opt-in. Cleanup is encouraged:
```js
  function demoPlugin(api) {
    const stop = api.subscribe(snapshot => log(snapshot));
    demoPlugin.cleanup = () => stop();
  }
```

Plugins don’t override Meat’s internal logic. They only extend via top-level mutation—and every mutation is tracked. No lifecycle traps. No hidden hooks. Just execution you control.

### Sandboxing & Isolation

Plugins operate inside sandboxed scopes. They don’t get to crawl internals. They don’t patch private memory. They behave like guests with a badge—not like root access.

Want to define a scoped method?
```js
  meat.foo = () => console.log("hello");
```
Meat tracks every top-level addition between use() and unuse(). If anything clashes:
```js
  console.warn("PLUGIN_CLASH: Method 'foo' already exists");
```
Plugins don’t overwrite silently. If they try, you’re alerted. It’s signal—not sabotage.

### Conflict Detection

Before plugin installs:
```js
  const before = Object.keys(meat);
```
After execution:
```js
  const after = Object.keys(meat);
```
Any overlaps that weren’t explicitly whitelisted emit a warning: `PLUGIN_CLASH`: Method "foo" overwritten.
<!-- Page Break --><div style="page-break-before: always;"></div>

You fix it by renaming, or by wrapping existing methods safely:
```js
  const original = meat.set;
  meat.set = (key, val) => {
    console.log("Plugin intercepted set");
    original(key, val);
  };
```
Plugins don’t compete. They collaborate—if invited.

### Adaptive Plugins

Plugins can check their mode and adjust behavior:
```js
  if (api.config.pluginAccess === "locked") {
    return; // exit quietly
  }
```
That makes them portable. A logging plugin can operate in full mode or read-only. A DOM plugin can bail if it’s restricted.

Plugins aren’t monoliths. They’re reactive utilities.

### Final Note

Meat’s plugin system isn’t declarative. It’s procedural. You load the plugin. You shape its scope. You control its teardown.

There’s no framework dance. Just behavior you invite.

If Chapter IV shaped signal, Chapter V shapes how external logic folds in—without folding the runtime.

Next up: Built-in Plugins like `persist()`, `linkToDOM()`, and `freeze`/`thaw` extensions that ship out of the box.
