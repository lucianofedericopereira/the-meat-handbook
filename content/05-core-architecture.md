---
title: Chapter II. The Forge of Runtime Precision
---

## Core Architecture

_Where abstraction melts. And signal takes shape._

If Chapter I was conviction in words, this is metal in motion. Meat isn’t a pattern—it’s a weapon. A precision tool, sharp in scope, and deadly in delivery. You don’t architect it like a palace. You wield it like a blade.

Meat wasn’t carved out of architectural purity. It was forged under real-world pressure—where structure is still fluid, but intent must land. That moment when you’re building alone, debugging under deadline, or shaping logic you’ll have to explain six months from now. Meat exists for that moment. The commits that happen before the debate. The code before the ceremony. It operates in that narrow space where clarity beats convention—and where the runtime must be an ally, not an obstacle.

Its internals reflect clarity at every turn. No hidden lifecycles. No tangled dependencies. No abstracted reactivity trees. What you see is what you control. And what you control responds exactly as expected.

Here’s how the forge burns.

### The State Engine

_Optional immutability. Permanent clarity._

State lives as a flat key-value object. There’s no reactivity theater, no nested proxies, no lifecycle negotiations. You own the shape and the behavior.

Mutation style is up to you:

- Immutable mode (`mutable: false`) — default safety. Every update clones the state, maintaining predictability and functional integrity.
- Mutable mode (`mutable: true`) — direct control. Changes happen in-place, perfect for lean flows and fast iterations.

Meat doesn’t enforce purity. It offers precision. You choose the metal, you swing the hammer.

### Methods to mold and inspect:

- `getState()` — full snapshot.
- `set(key, value)` — mutate and fire scoped event.
- `setState(updates)` — batch apply.
- `merge(obj)` — alias for fluid usage.
- `clear()` / `reset()` — purge or restore.
- `serialize()` — cached JSON string.
- `select(keys)` — filtered slices.
- `has(key)`, `hasChanged(key, value)` — inspection tools.
- `keys()`, `values()` — visibility.
- `find(fn)` — scoped key filtering.
- `dump()` — from persist plugin.
- `inspectKey(key)` — value + watch status.
- `isEmpty()` — forge idle check.

### Built-in emissions:

- `meat:update:{key}` — scoped reactivity.
- `meat:update` — full-state broadcast.

### Event Bus

_Synchronous. Scoped. Predictable._ Meat’s pub/sub system follows Mitt’s discipline but expands it into scoped reactivity and wildcard observability. Built on Map. Dispatch is synchronous, and feedback is traceable.

### Listener tools:

- `on(type, fn)` — subscribe to signal.
- `emit(type, payload)` — fire.
- `off(type, fn)` — disconnect.
- `onAny(fn)` — wildcard listener.
- `listeners()` — inspect active channels.
- `isWatched(type)` — scoped presence check.
- `unbindAll()` — global teardown.

### Reactivity flows:

- `watch(key, fn)` — scoped to data.
- `once(key, fn)` — single-use and auto-disconnect.
- `subscribe(fn)` — global heartbeat.

Handlers throw? You’ll know. `HANDLER_ERROR` and `WILDCARD_ERROR` show up in console—assuming `config.debug: true`.

### Plugin System

_Extend without bleed._ Plugins in Meat don’t hijack the runtime—they’re scoped, permissioned, and auditable.

#### Access modes:

- `open` — full API access.
- `restricted` — whitelist-defined surface.
- `locked` — read-only: state, serialization, and config.

You install with use(pluginFn, options), and revoke with unuse(plugin). Plugin conflict warnings show up as `PLUGIN_CLASH`, so nothing gets overwritten in silence.

### Built-in Plugins

_Useful. Isolated. Honest._

#### **persistPlugin**: Adds localStorage persistence and mutation tracking:

- `persist(key)` — save state to disk.
- `load(key)` — load from disk.
- `freeze()` / `thaw()` — toggle mutation mode.
- `lastModified()` — timestamp of change.
- `changedKeys()` — key-level diff log.
- `bindToGlobal(name)` — expose to global scope.
- `configurable()` — log config status.

Hooks into `set()` and `setState()` to track heat.

#### **linkPlugin**: Lightweight DOM binding without rendering engine:

- `linkToDOM(selector, attr)` — serialize state into an attribute.
- `unbindDOM()` — removes binding.

Useful for dashboards, visualizers, or zero-framework rendering.

### Config + Devtools

_Live introspection. No tooling required._

Config lives in `meat.config` and can be mutated directly.

Toggles & Tools:

- `config.mutable` — controls update behavior.
- `config.debug` — enables internal logging.
- `pluginAccess` — scopes plugin reach.
- `debugLog()` — gated internal messaging.
- `warn(code, detail)` — system messages.
- `version` — runtime version label.
- `devtools()` — logs current state as a table.
- `configurable()` — prints active config.

Everything observable. Nothing gated behind extensions.

### Runtime Composition

Shipped in ~300 lines. No build step. No scaffolding.

Meat loads via IIFE. No bundler required.

- Exports top-level meat object.
- Registers built-in plugins automatically.
- Can be dropped as script, added via CDN, or bundled by npm.
- `bindToGlobal()` makes it globally reachable for debugging.

There’s no hydration phase. No virtual abstraction. Just real reactive behavior you control, inspect, and extend.
