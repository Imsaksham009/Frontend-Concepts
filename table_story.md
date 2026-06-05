This is another strong project, but if an experienced frontend interviewer hears your current explanation, they'll immediately ask:

> Wait, is this virtualization or infinite scrolling?
>
> Why only 30 rows in DOM?
>
> Why keep 250 rows in memory?
>
> How did you handle real-time updates?
>
> How did you determine when to fetch the next batch?

So let's make the story more structured and technically sound.

---

## Situation

"We had a real-time monitoring table where data was continuously arriving from the backend. Over time, the number of records increased significantly and users could keep the application running for long periods.

Initially we were rendering all rows directly in the DOM. As the dataset grew, scrolling became less responsive and rendering performance degraded because the browser had to manage hundreds of DOM nodes while also processing continuous updates."

---

## Problem

"The main issue was that the table was receiving frequent updates and rendering large numbers of rows simultaneously.

Even though a user could only see a small portion of the table on the screen, React was still maintaining all rendered rows in the DOM."

---

## Solution

"We implemented row virtualization.

Instead of rendering all available rows, we only rendered the rows currently visible in the viewport plus a small overscan region."

Example:

```text
Total Rows Available: 500+

Rows In DOM:
30 visible
+ few buffer rows
```

---

## Data Management

"To avoid repeatedly requesting data during scrolling, we introduced a client-side buffer.

The browser maintained approximately 250 rows in memory.

When the user scrolled, the virtualization layer rendered only the rows currently visible on screen from this buffer."

```text
Server
   ↓
250 rows buffer
   ↓
30 visible rows rendered
```

---

## Lazy Loading

"When the user approached the end of the buffered dataset, we triggered a request for the next batch from the server.

This was handled using row indexes.

We tracked:

* current scroll position
* last loaded row index
* total buffered rows

Once the scroll position crossed a threshold, the next chunk was requested."

Example:

```text
Loaded: 0-250

User reaches row 220

Request:
251-500
```

---

## Real-Time Updates

This is the part interviewers will care about.

You need to explain what happened when new data arrived.

Possible answer:

"Incoming updates were merged into the buffered dataset rather than forcing a complete table refresh.

Since virtualization only renders visible rows, updates affecting off-screen rows did not trigger expensive DOM work."

This is a very strong statement.

---

## Result

"After virtualization, DOM size remained nearly constant regardless of dataset size.

We reduced rendering overhead significantly and maintained smooth scrolling even while processing continuous real-time updates."

---

# Follow-up Questions

### Why only 30 rows?

Good answer:

"The viewport could only display around 20-30 rows at a time. Rendering hundreds of rows provided no user benefit but increased DOM size and rendering cost."

---

### Why keep 250 rows in memory?

Good answer:

"It was a balance between memory usage and network requests. A larger buffer reduces fetch frequency and provides smoother scrolling, while still keeping memory consumption controlled."

---

### Virtualization vs Pagination?

Very common.

Pagination:

```text
Page 1
Page 2
Page 3
```

User changes pages.

Virtualization:

```text
Single continuous scroll
Only visible rows rendered
```

Better for monitoring dashboards.

---

### Virtualization vs Infinite Scroll?

This is important.

Virtualization:

```text
DOM optimization
```

Infinite Scroll:

```text
Data loading strategy
```

Your implementation actually used both:

* Virtualization for rendering
* Infinite loading for fetching

That's a senior-level answer.

---

### Why did performance improve?

Because DOM operations are expensive.

Without virtualization:

```text
500 rows
500 DOM nodes
500 React row components
```

With virtualization:

```text
500 rows in data
30 rows in DOM
```

React reconciliation becomes much cheaper.

---

### How would you implement this in React today?

Great answer:

"I would probably use react-window, react-virtualized, TanStack Virtual, or AG Grid's built-in virtualization depending on project requirements."

Notice something interesting?

This story directly connects to their JD because  **AG Grid heavily relies on virtualization internally** . If they ask:

> Have you used AG Grid?

You can honestly say:

> No, but I've built a custom virtualized table handling real-time updates and lazy loading. AG Grid solves many of the same challenges through its built-in row virtualization and data loading mechanisms.

That's a very strong bridge from your experience to their stack.
