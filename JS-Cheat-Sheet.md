# JavaScript Cheat Sheet

---

## 1. Execution Context

```javascript
// Three types of Execution Context:
// 1. Global EC — created when script starts
// 2. Function EC — created on every function call
// 3. Eval EC — created inside eval()

// Each EC has two phases:
// Creation Phase  → allocates memory, hoists declarations
// Execution Phase → runs code line by line

// Each EC has three components:
// Variable Environment → stores var, function declarations
// Lexical Environment  → stores let, const, function scope
// this binding         → determined by how function is called
```

---

## 2. Call Stack

```javascript
// LIFO — Last In, First Out
// Tracks WHERE we are in execution

function c() { return 'c'; }
function b() { return c(); }
function a() { return b(); }
a();

// Stack frames (top to bottom):
// [c] ← currently executing
// [b]
// [a]
// [Global]

// Stack Overflow — too many frames
function infinite() { return infinite(); }
infinite(); // RangeError: Maximum call stack size exceeded
```

---

## 3. Hoisting

```javascript
// var     → hoisted + initialized to undefined
// let/const → hoisted + NOT initialized (TDZ)
// function declarations → fully hoisted (name + body)
// function expressions  → only variable hoisted

console.log(a); // undefined — var hoisted
console.log(b); // ReferenceError — TDZ
console.log(fn); // [Function: fn] — fully hoisted

var a = 1;
let b = 2;
function fn() {}
```

---

## 4. Temporal Dead Zone (TDZ)

```javascript
// TDZ = period between hoisting and initialization
// Accessing let/const in TDZ throws ReferenceError

{
    // TDZ starts here for 'name'
    console.log(name); // ReferenceError
    let name = 'Peter'; // TDZ ends here
    console.log(name); // 'Peter'
}

// typeof also throws in TDZ (unlike var)
console.log(typeof undeclaredVar); // 'undefined' — safe
console.log(typeof tdzVar);        // ReferenceError
let tdzVar = 1;
```

---

## 5. var vs let vs const

```javascript
//              var         let         const
// Scope        function    block       block
// Hoisting     yes+init    yes(TDZ)    yes(TDZ)
// Re-declare   yes         no          no
// Re-assign    yes         yes         no
// Global prop  yes         no          no

// var leaks out of blocks
if (true) {
    var x = 1;   // accessible outside
    let y = 2;   // block scoped
    const z = 3; // block scoped
}
console.log(x); // 1
console.log(y); // ReferenceError
console.log(z); // ReferenceError

// const — binding is immutable, not value
const obj = { name: 'Peter' };
obj.name = 'Tony'; // allowed — mutating value
obj = {};          // TypeError — reassigning binding
```

---

## 6. Scope

```javascript
// Global Scope   → accessible everywhere
// Function Scope → accessible within function
// Block Scope    → accessible within {} (let/const only)

const global = 'global';

function outer() {
    const outerVar = 'outer';

    function inner() {
        const innerVar = 'inner';
        console.log(global);   // ✅ scope chain
        console.log(outerVar); // ✅ scope chain
        console.log(innerVar); // ✅ own scope
    }

    console.log(innerVar); // ❌ ReferenceError
}

// Scope chain — looks OUTWARD only, never inward
```

---

## 7. Lexical Scope

```javascript
// Scope determined by WHERE code is WRITTEN
// not WHERE it is called

const name = 'global';

function outer() {
    const name = 'outer';

    function inner() {
        // 'name' resolved at DEFINITION time
        // not at call time
        console.log(name); // 'outer' — always
    }

    return inner;
}

const fn = outer();
fn(); // 'outer' — not 'global'
// Even though fn is called in global scope
// it remembers where it was DEFINED
```

---

## 8. Closures

```javascript
// Inner function retains access to outer function's
// variables even after outer function returns

function makeCounter() {
    let count = 0; // closed over variable

    return {
        increment() { count++; },
        decrement() { count--; },
        getCount()  { return count; }
    };
}

const counter = makeCounter();
counter.increment();
counter.increment();
counter.getCount(); // 2

// count lives in heap — NOT garbage collected
// because closure still references it

// Classic loop bug
for (var i = 0; i < 3; i++) {
    setTimeout(() => console.log(i), 100);
} // 3, 3, 3 — all share same 'i'

for (let i = 0; i < 3; i++) {
    setTimeout(() => console.log(i), 100);
} // 0, 1, 2 — each iteration gets own 'i'
```

---

## 9. Event Loop

```javascript
// JavaScript is single-threaded
// Event loop allows non-blocking async behavior

// Order of execution:
// 1. Synchronous code (call stack)
// 2. Microtasks (Promise callbacks, queueMicrotask)
// 3. Macrotasks (setTimeout, setInterval, I/O)

console.log('1');

setTimeout(() => console.log('4'), 0); // macrotask

Promise.resolve()
    .then(() => console.log('3'));     // microtask

console.log('2');

// Output: 1, 2, 3, 4

// Rule: microtask queue fully drains before
// next macrotask is picked up
```

---

## 10. Microtasks vs Macrotasks

```javascript
// MICROTASKS (higher priority)
// → Promise .then() / .catch() / .finally()
// → queueMicrotask()
// → MutationObserver

// MACROTASKS (lower priority)
// → setTimeout
// → setInterval
// → setImmediate (Node.js)
// → I/O callbacks
// → UI rendering

setTimeout(() => console.log('macro'), 0);

queueMicrotask(() => console.log('micro 1'));

Promise.resolve()
    .then(() => console.log('micro 2'))
    .then(() => console.log('micro 3'));

// Output:
// micro 1
// micro 2
// micro 3
// macro ← macrotask runs only after ALL microtasks done
```

---

## 11. Callback Queue

```javascript
// Also called Task Queue / Macrotask Queue
// Web APIs place callbacks here after completion

// Flow:
// setTimeout(fn, 1000)
//     → Web API starts 1000ms timer
//     → JS engine continues executing
//     → After 1000ms, fn placed in Callback Queue
//     → Event Loop checks: is call stack empty?
//     → If yes AND microtask queue empty → push fn to stack

// Important: delay is MINIMUM, not exact
const start = Date.now();
setTimeout(() => {
    console.log(Date.now() - start); // 1000+ never exactly 1000
}, 1000);

// setTimeout(fn, 0) — not immediate
// Still goes through full async cycle
```

---

## 12. Promises

```javascript
// Three states: pending → fulfilled | rejected
// State transition is PERMANENT — once settled, never changes

const promise = new Promise((resolve, reject) => {
    // executor runs SYNCHRONOUSLY
    const success = true;

    if (success) {
        resolve('data');   // fulfilled
    } else {
        reject('error');   // rejected
    }
});

// Executor is sync — .then() callbacks are async (microtasks)
console.log('1');
Promise.resolve(2).then(v => console.log(v)); // runs after sync
console.log('3');
// Output: 1, 3, 2
```

---

## 13. Promise Chaining

```javascript
// .then() always returns a NEW Promise
// Return value of handler resolves that new Promise

fetch('/api/user')
    .then(res => res.json())          // return Promise → chain waits
    .then(user => user.name)          // return value  → resolves with value
    .then(name => name.toUpperCase()) // return value
    .catch(err => {
        // catches errors from ANY step above
        console.error(err);
        return 'default'; // returning RECOVERS the chain
    })
    .finally(() => {
        // always runs, doesn't receive value
        // return value ignored (unless throws)
        hideLoader();
    });

// Rejection propagates until caught
Promise.reject('error')
    .then(v => v)      // skipped
    .then(v => v)      // skipped
    .catch(e => e);    // 'error' — caught here
```

---

## 14. Promise.all

```javascript
// Resolves when ALL resolve → array of values (in order)
// Rejects IMMEDIATELY when ANY rejects

const p1 = Promise.resolve(1);
const p2 = new Promise(r => setTimeout(() => r(2), 1000));
const p3 = Promise.resolve(3);

Promise.all([p1, p2, p3])
    .then(([v1, v2, v3]) => console.log(v1, v2, v3));
// 1, 2, 3 — after 1 second (waits for slowest)

Promise.all([
    Promise.resolve(1),
    Promise.reject('error'), // ← this causes immediate rejection
    Promise.resolve(3)
]).catch(err => console.log(err)); // 'error'

// Non-Promise values wrapped in Promise.resolve()
Promise.all([1, 'hello', Promise.resolve(3)])
    .then(console.log); // [1, 'hello', 3]
```

---

## 15. Promise.allSettled

```javascript
// Waits for ALL to settle — never rejects
// Returns array of {status, value|reason}

Promise.allSettled([
    Promise.resolve(1),
    Promise.reject('error'),
    Promise.resolve(3)
]).then(results => {
    console.log(results);
    // [
    //   { status: 'fulfilled', value: 1      },
    //   { status: 'rejected',  reason: 'error'},
    //   { status: 'fulfilled', value: 3      }
    // ]

    // Useful for batch operations
    const succeeded = results.filter(r => r.status === 'fulfilled');
    const failed    = results.filter(r => r.status === 'rejected');
});
```

---

## 16. Promise.race

```javascript
// Resolves/rejects with FIRST settled Promise
// — whether fulfilled OR rejected

Promise.race([
    new Promise(r => setTimeout(() => r('slow'), 3000)),
    new Promise(r => setTimeout(() => r('fast'), 100)),
]).then(console.log); // 'fast'

// Classic use — timeout pattern
function withTimeout(promise, ms) {
    const timeout = new Promise((_, reject) =>
        setTimeout(() => reject(new Error(`Timeout: ${ms}ms`)), ms)
    );
    return Promise.race([promise, timeout]);
}

withTimeout(fetch('/api/data'), 5000)
    .then(handleData)
    .catch(err => console.log(err.message));
```

---

## 17. Promise.any

```javascript
// Resolves with FIRST fulfilled Promise
// Rejects only if ALL reject (AggregateError)
// Opposite of Promise.all

Promise.any([
    Promise.reject('err 1'),
    new Promise(r => setTimeout(() => r('second'), 500)),
    new Promise(r => setTimeout(() => r('third'), 100)),
]).then(console.log); // 'third' — first to FULFILL

// All reject → AggregateError
Promise.any([
    Promise.reject('err 1'),
    Promise.reject('err 2'),
]).catch(err => {
    console.log(err instanceof AggregateError); // true
    console.log(err.errors); // ['err 1', 'err 2']
});

// Use case — try multiple sources, use fastest
Promise.any([fetch(cdn1), fetch(cdn2), fetch(cdn3)]);
```

---

## 18. Async/Await

```javascript
// async → function always returns Promise
// await → pauses function execution until Promise settles

async function getUser(id) {
    try {
        const response = await fetch(`/api/users/${id}`);
        // function PAUSES here — control returns to event loop
        // resumes as microtask when Promise settles

        if (!response.ok) throw new Error(`HTTP ${response.status}`);

        return await response.json(); // also a Promise
    } catch(err) {
        console.error(err);
        throw err; // re-throw for caller to handle
    } finally {
        hideLoader(); // always runs
    }
}

// Sequential vs Parallel
async function sequential() {
    const a = await fetch('/api/a'); // waits
    const b = await fetch('/api/b'); // then waits
    // Total = timeA + timeB
}

async function parallel() {
    const [a, b] = await Promise.all([
        fetch('/api/a'), // both start
        fetch('/api/b')  // simultaneously
    ]);
    // Total = max(timeA, timeB)
}

// Async with loops
async function processAll(ids) {
    // Sequential loop
    for (const id of ids) {
        await processItem(id);
    }

    // Parallel — don't use forEach with async!
    await Promise.all(ids.map(id => processItem(id)));
}
```

---

## 19. Error Handling

```javascript
// Built-in Error Types
ReferenceError  // undeclared variable
TypeError       // wrong type
SyntaxError     // invalid syntax (parse time)
RangeError      // value out of range
Error           // base — use for custom errors

// try/catch/finally
try {
    riskyOperation();
} catch(err) {
    // handle specific types
    if (err instanceof TypeError) handleType(err);
    else throw err; // re-throw unknown errors
} finally {
    cleanup(); // ALWAYS runs — even if return in try
}

// Custom Error Classes
class AppError extends Error {
    constructor(message, code) {
        super(message);
        this.name = 'AppError';
        this.code = code;
    }
}

// Async error handling
async function main() {
    try {
        await riskyAsyncOperation();
    } catch(err) {
        // catches rejected Promises too
        console.error(err);
    }
}

// Unhandled rejections — always add .catch()!
fetch('/api').catch(console.error);
```

---

## 20. `this` Keyword

```javascript
// 'this' is determined by HOW function is called
// not WHERE it is written

// Rule 1 — Default binding (standalone call)
function show() { console.log(this); }
show(); // window (non-strict) | undefined (strict)

// Rule 2 — Implicit binding (method call)
const obj = {
    name: 'Peter',
    show() { console.log(this.name); }
};
obj.show(); // 'Peter' — obj is 'this'

// Rule 3 — Explicit binding
show.call(obj);    // obj is 'this'
show.apply(obj);   // obj is 'this'
const bound = show.bind(obj); // returns new fn with 'this' locked
bound(); // obj is 'this' permanently

// Rule 4 — new binding (highest priority)
function Person(name) { this.name = name; }
const p = new Person('Peter'); // 'this' = new empty object

// Arrow functions — NO own 'this'
// Inherits 'this' from lexical (surrounding) scope
const obj2 = {
    name: 'Peter',
    arrow: () => console.log(this.name), // 'this' = global
    regular() {
        const inner = () => console.log(this.name); // 'this' = obj2
        inner();
    }
};
```

---

## 21. call / apply / bind

```javascript
function greet(greeting, punctuation) {
    return `${greeting} ${this.name}${punctuation}`;
}

const peter = { name: 'Peter' };

// call — invoke immediately, args passed individually
greet.call(peter, 'Hello', '!');
// 'Hello Peter!'

// apply — invoke immediately, args passed as array
greet.apply(peter, ['Hello', '!']);
// 'Hello Peter!'

// bind — returns NEW function, 'this' permanently set
const greetPeter = greet.bind(peter, 'Hello');
greetPeter('!');  // 'Hello Peter!'
greetPeter('?');  // 'Hello Peter?'

// bind priority — beats implicit binding
const obj = { name: 'Tony', greet: greetPeter };
obj.greet('!'); // still 'Hello Peter!' — bind wins

// Practical use — preserving 'this' in callbacks
class Timer {
    constructor() { this.count = 0; }
    start() {
        setInterval(this.tick.bind(this), 1000);
        // OR: setInterval(() => this.tick(), 1000);
    }
    tick() { this.count++; }
}
```

---

## 22. Arrow Functions

```javascript
// Differences from regular functions:
// 1. No own 'this' — inherits lexically
// 2. No 'arguments' object
// 3. Cannot be used as constructor (no new)
// 4. No 'prototype' property

// Syntax
const add    = (a, b) => a + b;          // implicit return
const square = x => x * x;               // single param, no parens needed
const getObj = () => ({ name: 'Peter' }); // return object — wrap in parens
const multi  = (a, b) => {               // block body — needs explicit return
    const result = a + b;
    return result;
};

// 'this' is lexical
class Button {
    constructor() {
        this.count = 0;
    }

    // Arrow class field — 'this' permanently bound to instance
    handleClick = () => {
        this.count++; // always refers to Button instance
    }
}

// When NOT to use arrow functions
const objBad = {
    name: 'Peter',
    greet: () => console.log(this.name) // ❌ 'this' is global
};

const objGood = {
    name: 'Peter',
    greet() { console.log(this.name); } // ✅ 'this' is obj
};
```

---

## 23. Currying

```javascript
// Transform f(a, b, c) into f(a)(b)(c)
// Each call returns a new function expecting next argument

// Manual curry
const add = a => b => a + b;
const add5 = add(5);
add5(3); // 8
add5(10); // 15

// Generic curry implementation
function curry(fn) {
    return function curried(...args) {
        if (args.length >= fn.length) {
            return fn(...args); // all args received — execute
        }
        return (...more) => curried(...args, ...more); // wait for more
    };
}

const multiply = curry((a, b, c) => a * b * c);
multiply(2)(3)(4);  // 24
multiply(2, 3)(4);  // 24
multiply(2)(3, 4);  // 24
multiply(2, 3, 4);  // 24

// Real world use — reusable specialized functions
const filter = curry((pred, arr) => arr.filter(pred));
const map    = curry((fn, arr)   => arr.map(fn));

const getAdults    = filter(user => user.age >= 18);
const getNames     = map(user => user.name);

const adultNames = users => getNames(getAdults(users));
```

---

## 24. Memoization

```javascript
// Cache function results — same inputs return cached output
// Only works correctly with PURE functions

function memoize(fn) {
    const cache = new Map();

    return function(...args) {
        const key = JSON.stringify(args);

        if (cache.has(key)) {
            return cache.get(key); // return cached
        }

        const result = fn.apply(this, args);
        cache.set(key, result);
        return result;
    };
}

// Usage
function fibonacci(n) {
    if (n <= 1) return n;
    return fibonacci(n - 1) + fibonacci(n - 2);
}

const memoFib = memoize(fibonacci);
memoFib(40); // computed — slow
memoFib(40); // from cache — instant

// Memoize with expiry
function memoizeWithTTL(fn, ttl) {
    const cache = new Map();

    return function(...args) {
        const key   = JSON.stringify(args);
        const entry = cache.get(key);

        if (entry && Date.now() - entry.time < ttl) {
            return entry.value;
        }

        const value = fn.apply(this, args);
        cache.set(key, { value, time: Date.now() });
        return value;
    };
}
```

---

## 25. Deep Copy vs Shallow Copy

```javascript
const original = {
    name: 'Peter',
    address: { city: 'NYC' }, // nested object
    scores: [1, 2, 3]          // nested array
};

// SHALLOW COPY — new top-level, shared nested references
const shallow1 = { ...original };
const shallow2 = Object.assign({}, original);

shallow1.name = 'Tony';        // ✅ doesn't affect original
shallow1.address.city = 'LA';  // ❌ AFFECTS original — shared reference!

// DEEP COPY — completely independent at ALL levels

// Method 1: structuredClone (modern, recommended)
const deep1 = structuredClone(original);
deep1.address.city = 'LA'; // ✅ original untouched
// Handles: Date, Map, Set, RegExp, circular refs
// Cannot handle: functions, symbols

// Method 2: JSON (quick but limited)
const deep2 = JSON.parse(JSON.stringify(original));
// ❌ Loses: functions, undefined, Symbol, Date, Map, Set, Infinity, NaN

// Method 3: Custom recursive (handles everything)
function deepClone(value, visited = new Map()) {
    if (value === null || typeof value !== 'object') return value;
    if (visited.has(value)) return visited.get(value);

    const clone = Array.isArray(value) ? [] : {};
    visited.set(value, clone);

    for (let key in value) {
        if (value.hasOwnProperty(key)) {
            clone[key] = deepClone(value[key], visited);
        }
    }
    return clone;
}

// When to use what:
// Spread/{...}    → flat objects, no nested refs needed
// structuredClone → most cases, modern environments
// JSON method     → simple data, no functions/special types
// Custom clone    → need to handle functions or symbols
```

---

## 26. Object.freeze

```javascript
// Prevents: adding, removing, modifying properties
// Returns the same object (frozen)

const config = Object.freeze({
    apiUrl: 'https://api.example.com',
    timeout: 5000,
    nested: { retries: 3 } // ← NOT frozen (shallow!)
});

config.apiUrl  = 'changed';  // silently fails (throws strict mode)
config.newProp = 'added';    // silently fails
delete config.timeout;       // silently fails

console.log(config.apiUrl); // 'https://api.example.com' — unchanged

// SHALLOW — nested objects still mutable!
config.nested.retries = 99; // ✅ works — nested not frozen
console.log(config.nested.retries); // 99

// Deep freeze — freeze recursively
function deepFreeze(obj) {
    Object.getOwnPropertyNames(obj).forEach(name => {
        const value = obj[name];
        if (typeof value === 'object' && value !== null) {
            deepFreeze(value);
        }
    });
    return Object.freeze(obj);
}

// freeze vs seal vs preventExtensions
Object.freeze(obj);            // no add, no delete, no modify
Object.seal(obj);              // no add, no delete — CAN modify
Object.preventExtensions(obj); // no add — CAN delete and modify

// Check state
Object.isFrozen(obj);
Object.isSealed(obj);
Object.isExtensible(obj);
```

---

## 27. Array Methods

```javascript
const nums  = [1, 2, 3, 4, 5];
const users = [
    { name: 'Peter', age: 25, active: true  },
    { name: 'Tony',  age: 45, active: false },
    { name: 'Steve', age: 30, active: true  }
];

// MAP — transform every element, returns NEW array of same length
nums.map(n => n * 2);            // [2, 4, 6, 8, 10]
users.map(u => u.name);          // ['Peter', 'Tony', 'Steve']
users.map(u => ({ ...u, id: Math.random() })); // add id to each

// FILTER — keep elements that pass test, returns NEW array
nums.filter(n => n > 3);         // [4, 5]
users.filter(u => u.active);     // [Peter, Steve]
users.filter(u => u.age >= 30);  // [Tony, Steve]

// REDUCE — accumulate to single value
nums.reduce((acc, n) => acc + n, 0);  // 15 (sum)
nums.reduce((acc, n) => acc * n, 1);  // 120 (product)

// Group by
users.reduce((groups, user) => {
    const key = user.active ? 'active' : 'inactive';
    groups[key] = [...(groups[key] || []), user];
    return groups;
}, {}); // { active: [Peter, Steve], inactive: [Tony] }

// FIND — first element that passes (or undefined)
users.find(u => u.age > 30);     // Tony object
nums.find(n => n > 10);          // undefined

// SOME — true if AT LEAST ONE passes
nums.some(n => n > 4);           // true
users.some(u => u.age > 50);     // false

// EVERY — true if ALL pass
nums.every(n => n > 0);          // true
nums.every(n => n > 3);          // false

// FLAT — flatten nested arrays
[1, [2, [3, [4]]]].flat();       // [1, 2, [3, [4]]] depth 1
[1, [2, [3, [4]]]].flat(2);      // [1, 2, 3, [4]]
[1, [2, [3, [4]]]].flat(Infinity); // [1, 2, 3, 4]

// FLATMAP — map then flat(1) — more efficient
['hello world', 'foo bar'].flatMap(s => s.split(' '));
// ['hello', 'world', 'foo', 'bar']

[1, 2, 3].flatMap(n => [n, n * 2]);
// [1, 2, 2, 4, 3, 6]

// CHAINING — combine methods
const result = users
    .filter(u => u.active)        // [Peter, Steve]
    .map(u => u.name)             // ['Peter', 'Steve']
    .reduce((str, name) =>
        str ? `${str}, ${name}` : name, '');
// 'Peter, Steve'
```

---

## Quick Reference Card

```javascript
// ASYNC PRIORITY ORDER
// Sync code → Microtasks → Macrotasks

// THIS BINDING PRIORITY
// new → explicit (call/apply/bind) → implicit (obj.fn()) → default

// COPY METHODS COMPARISON
// {...obj}         → shallow
// Object.assign    → shallow
// structuredClone  → deep (no functions)
// JSON parse/str   → deep (loses special types)
// custom recursive → deep (handles everything)

// PROMISE STATIC METHODS
// Promise.all         → all fulfill or first reject
// Promise.allSettled  → all settle, never rejects
// Promise.race        → first to settle (fulfill or reject)
// Promise.any         → first to fulfill or all reject

// ARRAY METHOD RETURNS
// map        → new array (same length)
// filter     → new array (shorter or equal)
// reduce     → single value
// find       → element or undefined
// findIndex  → index or -1
// some       → boolean
// every      → boolean
// flat       → new array (flattened)
// flatMap    → new array (mapped + flattened 1 level)
// forEach    → undefined (side effects only)
```
