---
title: Chapter XI. Learn by Doing (DEFCON 1).
---

## Learn by Doing (DEFCON 1)

Extreme, high-stakes examples where signal failure has dire consequences.

### 1. Atomic Transaction Across Multiple Keys
```js
meat.safe(() => {
  const { balance, history } = meat.getState().bank;
  const amount = 100;
  meat.set("bank.balance", balance - amount);
  meat.set("bank.history", [...history, { debit: amount }]);
  if (meat.get("bank.balance") < 0) throw new Error("Overdraw!");
});
```

### 2. Circuit Breaker Plugin
```js
function breakerPlugin(api, options = { threshold: 5 }) {
  let failures = 0;
  api.watch("errors", () => {
    failures++;
    if (failures >= options.threshold) {
      api.set("circuitOpen", true);
    }
  });
}
meat.use(breakerPlugin);
```
<!-- Page Break --><div style="page-break-before: always;"></div>

### 3. Fail-Safe WebSocket Sync
```js
const ws = new WebSocket("wss://example.com/sync");
ws.onmessage = e => meat.merge(JSON.parse(e.data));
meat.onAny((type, payload) => {
  if (ws.readyState === WebSocket.OPEN) {
    ws.send(JSON.stringify({ type, payload }));
  }
});
ws.onerror = () => meat.set("syncError", true);
```

### 4. Offline Queue with Exponential Backoff
```js
const queue = [];
window.addEventListener("offline", () => meat.set("offline", true));
window.addEventListener("online", () => {
  meat.set("offline", false);
  processQueue();
});
function enqueueChange(key, value) {
  queue.push({ key, value });
}
function processQueue(attempt = 1) {
  if (!queue.length) return;
  const item = queue.shift();
  meat.set(item.key, item.value);
  fakeSync(item)
    .then(() => processQueue(1))
    .catch(() => {
      queue.unshift(item);
      setTimeout(() => processQueue(attempt + 1), Math.pow(2, attempt) * 1000);
    });
}
```

### 5. Collaborative Cursor Sync
```js
const channel = new BroadcastChannel("cursor-sync");
meat.watch("cursor", pos => channel.postMessage(pos));
channel.onmessage = e => meat.set("remoteCursor", e.data);
```

### 6. Dynamic Feature Flags
```js
function featureFlagPlugin(api, flagsUrl) {
  fetch(flagsUrl)
    .then(res => res.json())
    .then(flags => api.set("featureFlags", flags));
  api.watch("featureFlags", flags => {
    Object.entries(flags).forEach(([name, enabled]) => {
      api.set(`flags.${name}`, enabled);
    });
  });
}
meat.use(featureFlagPlugin, "https://example.com/flags.json");
```

### 7. GDPR History Scrub Plugin
```js
function scrubPlugin(api, keysToRedact = []) {
  api.use(MeatChronicle);
  api.watch("history", hist => {
    const scrubbed = hist.map(record => {
      keysToRedact.forEach(k => delete record.payload[k]);
      return record;
    });
    api.set("history", scrubbed);
  });
}
meat.use(scrubPlugin, ["email", "ssn"]);
```

### 8. Snapshot Isolation Read-Only View
```js
const snapshot = meat.serialize();
function readOnlyView() {
  const state = JSON.parse(snapshot);
  return state; // safe read without affecting live state
}
```

### 9. Hierarchical State Partitioning
```js
function partitionPlugin(api) {
  const modules = Object.keys(api.getState());
  modules.forEach(mod => {
    api.watch(mod, state => {
      api.set(`partitions.${mod}`, state);
    });
  });
}
meat.use(partitionPlugin);
```

### 10. On-Demand Component Loader
```js
meat.watch("loadComponent", id => {
  import(`./components/${id}.js`)
    .then(mod => mod.mount(meat))
    .catch(err => meat.set("loadError", { id, err }));
});
```
