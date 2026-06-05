For your experience level and this role, I would focus on  **React rendering, re-rendering, performance, and state management** . That's where interviewers usually go after basic React questions.

# Tier 1 (Highest Probability)

These are almost guaranteed.

---

## 1. What causes a React component to re-render?

Answer:

* State changes
* Parent re-renders
* Context value changes
* Props reference changes

Follow-up:

### If parent re-renders, does child always re-render?

Answer:

> By default yes. React will call the child component again. React.memo can prevent unnecessary re-renders if props are unchanged.

---

## 2. React.memo

### What is React.memo?

Answer:

> React.memo is a higher-order component that memoizes a component and skips re-rendering when props remain unchanged.

```jsx
export default React.memo(MyComponent);
```

---

### When does React.memo fail?

This is a favorite question.

```jsx
<Child data={{ id: 1 }} />
```

Every render:

```js
{ id: 1 }
```

creates a new object reference.

React.memo sees:

```text
oldRef !== newRef
```

Child re-renders.

---

### When did you use React.memo?

You already have a real answer:

> During memory optimization of the chart application we found unnecessary child component re-renders through React Profiler. React.memo helped prevent re-renders for components whose props did not actually change.

---

## 3. useMemo

### What is useMemo?

Answer:

> It memoizes the result of an expensive computation.

```jsx
const filteredData = useMemo(() => {
   return processData(data);
}, [data]);
```

---

### Why not use useMemo everywhere?

Good question.

Answer:

> useMemo itself has a cost. If the computation is cheap, memoization can actually reduce performance.

---

### Real example from your project?

Answer:

> Large chart datasets, filtering operations, derived calculations, row processing logic.

---

## 4. useCallback

### What is useCallback?

Answer:

> It memoizes a function reference.

```jsx
const handleClick = useCallback(() => {
   ...
}, []);
```

---

### Difference between useMemo and useCallback?

Answer:

```text
useMemo → Memoizes Value

useCallback → Memoizes Function
```

---

### Why use useCallback?

Answer:

> To prevent unnecessary child re-renders when passing callback props to memoized components.

---

### Example

Bad:

```jsx
<Child
  onClick={() => {}}
/>
```

New function every render.

Good:

```jsx
const onClick = useCallback(() => {}, []);

<Child onClick={onClick} />
```

---

## 5. useRef

This is very important for your projects.

---

### What is useRef?

Answer:

> useRef stores mutable values without triggering re-renders.

---

### Difference between useState and useRef?

| useState           | useRef          |
| ------------------ | --------------- |
| Triggers re-render | No re-render    |
| UI state           | Mutable storage |

---

### When did you use useRef?

You have excellent real examples:

```text
Chart Instance
WebSocket Reference
Pending Packet Queue
Scroll Position
```

---

### Why useRef instead of useState?

Answer:

> Because updates were high frequency and did not require UI updates. Using state would have triggered unnecessary React renders.

This answer directly maps to your virtual table.

---

# Tier 2 (Very Common)

---

## 6. React Rendering Lifecycle

```text
State Change
      ↓
Render Phase
      ↓
Reconciliation
      ↓
Commit Phase
      ↓
DOM Update
```

Know this flow.

---

## 7. Reconciliation

### What is reconciliation?

Answer:

> React compares old Virtual DOM and new Virtual DOM and updates only changed nodes.

---

## 8. Keys

### Why keys?

Answer:

> Keys help React identify elements efficiently during reconciliation.

---

### Why not use index as key?

Answer:

> When list order changes, React may reuse incorrect components leading to bugs and unnecessary renders.

---

## 9. Context vs Redux

Very common.

Answer:

Context:

```text
State Sharing
```

Redux:

```text
State Management
Middleware
DevTools
Scalability
```

---

# Tier 3 (React 18)

---

## Automatic Batching

Before:

```jsx
setA();
setB();
```

Multiple renders possible.

React 18:

```text
Single render
```

---

## Concurrent Rendering

Know conceptually:

> React can interrupt and prioritize rendering work to keep UI responsive.

---

## Suspense

```jsx
<Suspense fallback={<Loader />}>
   <Dashboard />
</Suspense>
```

---

# Performance Scenarios

These are exactly the questions I'd ask you.

---

## User says application becomes slow after 4 hours.

How do you debug?

Answer:

1. React Profiler
2. Memory Tab
3. Heap Snapshots
4. Check listeners
5. Check WebSockets
6. Check renders

---

## Chart receives 500 datapoints/sec.

How do you handle it?

Your answer:

* Bounded window
* Replace instead of accumulate
* Chart.update()
* Zoom-based fetching

---

## Table receives 300k rows.

How do you handle it?

Your answer:

* Virtualization
* Lazy Loading
* Buffering
* Fetch by range

---

# The 10 React Questions You Must Nail

1. What causes React re-renders?
2. React.memo?
3. useMemo?
4. useCallback?
5. useRef?
6. useState vs useRef?
7. Why did you reduce memory from 2GB to 500MB?
8. How did React Profiler help?
9. How would you optimize a real-time table?
10. How would you optimize a real-time chart?

If you can answer those deeply with examples from your own projects, you'll be stronger than most 2-3 YOE frontend candidates because you're answering from production experience rather than textbook definitions.
