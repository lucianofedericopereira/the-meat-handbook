---
title: Chapter X. Learn by Doing (Ninja).
---

## Learn by Doing (Ninja)

In this tier, we explore signal architecture with velocity. These patterns aren’t just reactive—they’re predictive, protective, and composable at runtime.

---

### Multi-Key Proxy Binding

```js
function proxyPlugin(api) {
  ["firstName", "lastName"].forEach(key => {
    api.watch(key, () => {
      const full = api.get("firstName") + " " + api.get("lastName");
      api.set("fullName", full);
    });
  });
}

meat.use(proxyPlugin);
```

---

### Timebox + Revert Mechanism

```js
function timeboxPlugin(api) {
  api.set("temp", true);
  setTimeout(() => {
    api.undo("temp");
  }, 2000);
}

meat.use(timeboxPlugin);
```

---

### Deep Signal Replication Across Tabs

```js
const tabChannel = new BroadcastChannel("meat-sync");

meat.subscribe(snapshot => {
  tabChannel.postMessage(snapshot);
});

tabChannel.onmessage = e => {
  if (e.data._source !== location.href) meat.merge(e.data);
};
```

---

### Conditional Plugin Injection

```js
if (window.location.pathname === "/admin") {
  meat.use(adminAuditPlugin);
}
```

---

### Self-Removing Watcher

```js
meat.watch("count", val => {
  if (val > 100) {
    console.warn("Too high!");
    return () => false; // teardown immediately
  }
});
```

---

### Reactive Pagination Cursor

```js
meat.watch("page", p => {
  fetch(`/api/data?page=${p}`)
    .then(res => res.json())
    .then(items => meat.set("items", items));
});
```

---

### Mutation-Based Trigger Chain

```js
meat.watch("step", step => {
  switch (step) {
    case 1: meat.set("message", "Init"); break;
    case 2: meat.set("message", "Running"); break;
    case 3: meat.set("complete", true); break;
  }
});
```

---

### Plugin Triggered by External Signal

```js
window.addEventListener("message", e => {
  if (e.data.signal) {
    meat.set("remoteSignal", e.data.signal);
  }
});
```

---

### Recursive Key Graph Explorer

```js
function graphExplorer(api) {
  function explore(obj) {
    Object.keys(obj).forEach(k => {
      api.watch(k, val => {
        console.log(`${k}:`, val);
        if (typeof val === "object") explore(val);
      });
    });
  }
  explore(api.getState());
}

meat.use(graphExplorer);
```

---

Signal fluency isn’t about memorizing tools. It’s about composing movement.  
These are signal kata—train until they’re reflex.
