# `Object.deepEqual`

A TC39 proposal to add `Object.deepEqual`: a standard method for deep structural equality checks between two values in JavaScript. It compares objects, arrays, maps, sets, and other types recursively, handling edge cases like circular references and NaN correctly. This aims to eliminate the need for external libraries like Lodash's `_.isEqual`, offering a consistent and native solution for deep comparisons.

## Status

Stage: 0
Champions: TBD
Authors: Mikhail Efanov (@misha98857)

---

## Motivation

JavaScript currently lacks a standard built-in way to perform deep structural comparisons between objects. Developers are forced to rely on third-party libraries such as Lodash (`_.isEqual`), deep-equal, or implement their own recursive comparison logic, which can be error-prone, inconsistent, and inefficient.

Adding a native `Object.deepEqual` would:

- Provide a canonical, spec-compliant, and optimized way to check deep equality.
- Improve ergonomics by covering a common use case without third-party dependencies.
- Enhance code clarity by expressing deep comparison intent explicitly.
- Align JavaScript with other major programming languages that offer deep equality out of the box.

---

## Proposal

Introduce a static method `Object.deepEqual(a, b)` that returns `true` if the inputs are deeply structurally equal, and `false` otherwise.

```js
Object.deepEqual({ a: 1, b: [2, 3] }, { a: 1, b: [2, 3] }); // true
Object.deepEqual({ a: 1 }, { a: 1, b: 2 }); // false
Object.deepEqual(NaN, NaN); // true
Object.deepEqual(Object(1), Object(1)); // true
Object.deepEqual([1, { x: 2 }], [1, { x: 2 }]); // true
```

---

## Semantic

- Performs a deep, recursive comparison of enumerable own properties.
- Follows the SameValueZero algorithm for primitive equality.
- Handles objects, arrays, typed arrays, Maps, Sets, Dates, RegExps, and boxed primitives.
- Correctly handles circular references.
- Does not compare prototype chains, property descriptors, or non-enumerable properties.
- Does not invoke getters or consider symbol properties (TBD).

---

## Edge Cases

```js
Object.deepEqual(new Set([1, 2]), new Set([2, 1])); // true
Object.deepEqual({ a: NaN }, { a: NaN }); // true
Object.deepEqual(new Date("2023-01-01"), new Date("2023-01-01")); // true
Object.deepEqual({ x: undefined }, {}); // false
```

---

## Circular References

```js
const a = {};
a.self = a;
const b = {};
b.self = b;
Object.deepEqual(a, b); // true
```

--- 

## Prior Art

- _.isEqual (Lodash)
- assert.deepEqual (Node.js)
- fast-deep-equal, deep-equal, ramda.equals, deep-equal
- Python’s == for dicts/lists, Java’s .equals(), Rust’s PartialEq

## Alternatives Considered

- Adding .deepEquals() as a method on Object.prototype (risk of collisions and pollution).
- Adding a new global function deepEqual(a, b) (less discoverable and inconsistent with existing Object.* methods).
- Using JSON.stringify for deep comparison (fails with functions, undefined, circular structures, non-enumerables, etc.)

## API

```js
namespace Object {
  function deepEqual(a: unknown, b: unknown): boolean;
}
```

## Polyfill

TBD
