# Node.js Event Loop Phases

**Date:** 2026-03-14
**Category:** nodejs

## What I Learned

The Node.js event loop has 6 phases that execute in order:

```
   ┌───────────────────────────┐
┌─>│         timers            │  ← setTimeout, setInterval callbacks
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │     pending callbacks     │  ← I/O callbacks deferred to next loop
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │       idle, prepare       │  ← internal use only
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │           poll            │  ← fetch new I/O events (blocking here)
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │           check           │  ← setImmediate callbacks
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
└──┤      close callbacks      │  ← socket.on('close', ...)
   └───────────────────────────┘
```

**Key insight:** `process.nextTick()` and Promises run between *every* phase — not inside any phase.

## Code Example

```js
setTimeout(() => console.log('1 - timer'), 0);
Promise.resolve().then(() => console.log('2 - promise'));
process.nextTick(() => console.log('3 - nextTick'));
setImmediate(() => console.log('4 - immediate'));

// Output order:
// 3 - nextTick   (microtask, runs first)
// 2 - promise    (microtask, runs second)
// 1 - timer      (timer phase)
// 4 - immediate  (check phase)
```

## Why It Matters

Understanding phase order helps you avoid subtle async bugs in high-load APIs.

## Reference

- [Node.js docs — event loop](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick)
