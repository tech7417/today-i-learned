# Optional Chaining — Advanced Tips

**Date:** 2026-03-14
**Category:** javascript

## What I Learned

Optional chaining `?.` works for more than just property access — it also works for method calls and array indexing.

## Code Examples

```js
// Property access
const city = user?.address?.city;

// Method call — won't throw if method doesn't exist
const upper = str?.toUpperCase();

// Array index
const first = arr?.[0];

// Function call
const result = callback?.();

// Combined with nullish coalescing
const name = user?.profile?.name ?? 'Anonymous';
```

## Common Mistake

```js
// WRONG — this still throws if user is null
const city = user.address?.city;

// RIGHT
const city = user?.address?.city;
```

## Performance Note

Optional chaining compiles down to ternary checks in older targets — not free, but negligible in most cases.
