## Event listeners

Node.js offers the `EventEmitter` class, which we'll use to handle our events.

**Methods:**
- `emit` is used to trigger an event
- `on` is used to add a callback function that's going to be executed when the event is triggered
- `once()`: add a one-time listener
- `removeListener()` / `off()`: remove an event listener from an event
- `removeAllListeners()`: remove all listeners for an event

The `EventEmitter` calls all listeners synchronously in the order in which they were registered.

```js
const EventEmitter = require('events');
const myEmitter = new EventEmitter();

myEmitter.on('event', () => {
    console.log('Listener 1 executed');
});

myEmitter.on('event', () => {
    console.log('Listener 2 executed');
});

console.log('Before emitting event');
myEmitter.emit('event');  // Listeners execute synchronously
console.log('After emitting event');
```

**Output:**

```
Before emitting event
Listener 1 executed
Listener 2 executed
After emitting event
```

---
## process.nextTick()

Every time the runtime calls back into JavaScript for an event, we call it a tick.

When we pass a function to `process.nextTick()`, we instruct the engine to invoke this function immediately after the current operation completes, before moving to the next phase in the event loop.

