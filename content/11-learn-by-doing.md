---
title: Chapter VIII. Learn By Doing.
---

## Learn by Doing.

This chapter breaks form. Instead of narrative, it’s raw signal. Dozens of examples—meant to be tested, remixed, broken, and reused. No abstraction, no scaffolding. Just direct hits from the forge.

---


### Basic Wiring

```js
meat.set("ready", true);
console.log(meat.get("ready"));

meat.subscribe(state => {
  console.log("Snapshot:", state);
});

meat.watch("theme", t => {
  applyTheme(t);
});

meat.once("user", u => {
  greet(u.name);
});

meat.onAny((type, payload) => {
  console.log("EVENT:", type, payload);
});
```

<!-- Page Break --><div style="page-break-before: always;"></div>

### Batch Changes

```js
meat.setState({ theme: "light", locale: "en" });

const profile = {
  name: "Luciano",
  age: 32,
  online: true,
};
meat.merge(profile);
```

---


### DOM Binding

```js
meat.linkToDOM("#app", "data-state");
meat.set("theme", "dark");
// <div id="app" data-state='{"theme":"dark"}'></div>
```

---


### Form Sync

```js
const input = document.querySelector("#email");
input.addEventListener("input", e => {
  meat.set("email", e.target.value);
  meat.persist();
});

window.addEventListener("load", () => {
  meat.load();
});
```

---


### Undo + Rollback

```js
meat.use(MeatChronicle);
meat.set("count", 1);
meat.set("count", 2);
meat.undo("count");         // back to 1
meat.rollbackAll();         // restores all keys
```

---


### Protect Block

```js
meat.safe(() => {
  meat.set("score", null);
  throw new Error("Game broke");
});
// score gets rolled back
```

---


### Change Audit

```js
meat.set("language", "en");
meat.set("theme", "dark");

console.log(meat.changedKeys());  // ["language", "theme"]
console.log(meat.lastModified()); // timestamp
```

<!-- Page Break --><div style="page-break-before: always;"></div>


### Devtools

```js
meat.devtools();     // console.table of state
meat.logState();     // from logStatePlugin
```

---


### Listener Teardown

```js
const stop = meat.watch("visible", val => toggle(val));
stop(); // remove listener
```

---


### Plugin: Timestamp Injection

```js
function timestampPlugin(api) {
  api.setTimestamp = () => {
    api.set("timestamp", Date.now());
  };
}

meat.use(timestampPlugin);
meat.setTimestamp();
```

<!-- Page Break --><div style="page-break-before: always;"></div>


### Plugin: Mutation Counter

```js
function mutationCounter(api) {
  let count = 0;
  api.subscribe(() => count++);
  api.getMutationCount = () => count;
  api.resetCount = () => count = 0;
}

meat.use(mutationCounter);
console.log("Mutations:", meat.getMutationCount());
```

---


### Plugin: Logger

```js
function loggerPlugin(api) {
  api.onAny((type, payload) => {
    console.log(`[LOG] ${type}`, payload);
  });
}

meat.use(loggerPlugin);
```

---


### Serialize + Persist

```js
const snapshot = meat.serialize();
localStorage.setItem("state", snapshot);
```

---


### Hydrate on Load

```js
window.addEventListener("load", () => {
  const data = localStorage.getItem("state");
  if (data) {
    meat.merge(JSON.parse(data));
  }
});
```

---


### Reactive Canvas

```js
const canvas = document.getElementById("draw");
const ctx = canvas.getContext("2d");

meat.watch("angle", deg => {
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  drawPointer(ctx, deg);
});
```

---


### State-Based Styling

```js
meat.watch("theme", theme => {
  document.body.classList.toggle("dark", theme === "dark");
});
```

<!-- Page Break --><div style="page-break-before: always;"></div>


### Autosave Draft

```js
meat.watch("draft", val => {
  localStorage.setItem("draft", val);
});
```

---


### Plugin: Visualizer

```js
function signalVisualizer(api) {
  api.subscribe(snapshot => {
    document.title = `Keys: ${Object.keys(snapshot).length}`;
  });
}

meat.use(signalVisualizer);
```

---


### Reactive Network

```js
meat.watch("query", q => {
  fetch(`/search?q=${q}`)
    .then(res => res.json())
    .then(results => meat.set("results", results));
});
```

<!-- Page Break --><div style="page-break-before: always;"></div>


### Reactive System Time

```js
setInterval(() => {
  meat.set("now", Date.now());
}, 1000);
```

---


### Event Audit UI

```js
meat.onAny((type, payload) => {
  const el = document.createElement("div");
  el.textContent = `[${type}]: ${JSON.stringify(payload)}`;
  document.body.appendChild(el);
});
```

---


### Reactive Debug Overlay

```js
const debug = document.createElement("pre");
document.body.appendChild(debug);

meat.subscribe(state => {
  debug.textContent = JSON.stringify(state, null, 2);
});
```

<!-- Page Break --><div style="page-break-before: always;"></div>


### Live Field Diff

```js
const prev = meat.getState();
meat.set("score", 20);
const next = meat.getState();

const diff = Object.keys(next).filter(k => prev[k] !== next[k]);
console.log("Changed:", diff);
```

---


### Reactive Input Binding

```js
document.getElementById("message").addEventListener("input", e => {
  meat.set("message", e.target.value);
});

meat.watch("message", m => {
  console.log("Input:", m);
});
```

---


### Full State Dump on Ctrl+D

```js
document.addEventListener("keydown", e => {
  if (e.ctrlKey && e.key === "d") {
    meat.devtools();
  }
});
```
