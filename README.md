# Frontend Interview — Deep Dive Notes

A structured, in-depth set of notes for mastering frontend concepts from first principles. Every topic is written to build a real mental model, not just memorize answers.

> Tip: every note file has a **Table of Contents** at the top and a **← Back to README** link so you can jump around quickly.

---

## 🗺️ Start Here

- [Frontend Interview Roadmap](0.%20strategy.md) — the overall strategy and study plan.

---

## 📜 JavaScript

Deep dives into how JavaScript actually works under the hood.

| #  | Topic | What's inside |
| -- | ----- | ------------- |
| 1  | [The JavaScript Engine](JS/1.%20The%20JavaScript%20Engine.md) | Parsing, JIT compilation, optimization/deoptimization |
| 2  | [Memory Management](JS/2.%20Memory%20Management.md) | Call stack, heap, stack overflow, garbage collection, memory leaks |
| 3  | [Async JS + Execution Flow](JS/3.%20Async%20JS%20Func%20+%20execution%20flow.md) | Sync vs async, event loop, micro/macrotasks, `setTimeout`, `requestAnimationFrame` |
| 4  | [Types and Built-in Objects](JS/4.%20types%20and%20built-in%20Object.md) | Types, coercion, shallow vs deep copy, `Map`/`Set`/`WeakMap`/`WeakSet` |
| 5  | [Functions](JS/5.%20functions.md) | Invocation patterns, arguments, arrow functions, HOFs, first-class functions, composition |
| 6  | [`this` Keyword and Context](JS/6.%20this%20Keyword%20and%20Context.md) | The four binding rules, arrow functions, `this` in classes & special cases |
| 7  | [Hoisting & TDZ](JS/7.%20Hoisting%20&%20TDZ.md) | `var`/`let`/`const` hoisting, the Temporal Dead Zone, function hoisting |
| 8  | [Scope + Closures](JS/8.%20Scope%20+%20Closures.md) | Scope chain, closures, patterns (module, factory, memoization, debounce/throttle) |
| 9  | [Context vs Scope and Use Strict](JS/9.%20Context%20vs%20Scope%20and%20Use%20Strict.md) | _(placeholder — to be written)_ |
| 10 | [Prototypes and the Prototype Chain](JS/10.%20Prototypes%20and%20the%20Prototype%20Chain.md) | `__proto__` vs `prototype`, inheritance, `Object.create`, monkey patching |
| 11 | [OOPs](JS/11.%20OOPs.md) | Classes, getters/setters, static, private fields, inheritance, the four pillars, mixins |

---

## 💼 Resume-Related Deep Dives

Topics tied to real project experience — likely interview talking points.

| Topic | What's inside |
| ----- | ------------- |
| [React](Resume-related/react.md) | Re-renders, `memo`/`useMemo`/`useCallback`/`useRef`, reconciliation, React 18 features |
| [Chart Story](Resume-related/chart_story.md) | Chart.js, where to store chart data, zoom/pan, point limits |
| [Table Story](Resume-related/table_story.md) | Virtual table design and trade-offs |
| [Table Story Package](Resume-related/table_story_package.md) | Packaging the table component |
| [Heap Snapshot & Profiler](Resume-related/heap_snapshot_profiler.md) | Memory snapshots, retained/shallow size, detached DOM, React Profiler |
| [Memory Deduction](Resume-related/Memory%20Deduction.md) | Reasoning about memory usage |
| [Electron](Resume-related/electron.md) | Common Electron interview questions |
| [WebSocket](Resume-related/web%20socket.md) | WebSocket fundamentals |
| [WS Questions](Resume-related/ws%20Questions.md) | WebSocket interview questions |
| [Testing](Resume-related/testing.md) | Testing approach and tooling |

---

## How to use these notes

1. Follow the [roadmap](0.%20strategy.md) for study order.
2. Open a topic — read the **Table of Contents** first to see the shape of it.
3. Use the in-file anchor links to jump to a specific sub-topic.
4. Hit **← Back to README** anytime to return here.
