Your chart story is actually becoming quite strong now because it's not just "I rendered a chart". You solved a real-time data visualization problem.

---

## Why Chart.js?

I would slightly improve your answer:

> The project already had a mature Chart.js implementation and was running on React 16. Migrating to a different charting library would have required significant effort and risk. Chart.js was already meeting our functional requirements, so instead of replacing the library, we focused on optimizing its rendering and memory usage for larger datasets and longer running sessions.

This sounds much more like an engineering decision.

---

# Why not store everything in React state?

This is where many React developers fail.

Bad answer:

> Because it was slow.

Good answer:

> We were receiving hundreds of data points per second. If every incoming data point updated React state, React would continuously enter the render cycle and attempt reconciliation. Since the chart itself was rendering through Chart.js on a canvas, React did not need to participate in rendering every individual data point. We only needed React for UI-level state while the high-frequency chart data was managed outside React's rendering lifecycle.

This answer will impress experienced frontend engineers.

---

# Where should chart data be stored?

There are three possibilities.

## Option 1: React State

```tsx
const [data, setData] = useState([]);
```

Good for:

* Small datasets
* Low update frequency

Bad for:

```text
500+ points/sec
24 hours
```

Because every update triggers React renders.

---

## Option 2: useRef

```tsx
const dataRef = useRef([]);
```

Good for:

* Mutable data
* High-frequency updates

Does not trigger re-render.

Very common in trading dashboards and monitoring tools.

---

## Option 3: MobX Store

This sounds closest to what you actually did.

Example:

```ts
class ChartStore {
   @observable points = [];
}
```

Chart component subscribes only when needed.

Updates happen in MobX.

Chart.js consumes data from store.

React doesn't re-render for every point.

---

## Interview Answer

If interviewer asks:

> Where were data points stored?

I would answer:

> We maintained the chart data inside MobX rather than React component state. The incoming stream was high frequency, so keeping every point in React state would have caused excessive renders. MobX acted as the data source while Chart.js handled the actual canvas rendering. React was mainly responsible for UI interactions such as zooming, controls, and configuration.

This is probably the most accurate answer.

---

# Your 2000-point limit

This is actually a very good optimization.

Interview answer:

> To prevent unbounded growth, we maintained a sliding window. The chart displayed a maximum of around 2000 points. Once that threshold was reached, older points were removed and the visible dataset was downsampled while preserving the overall shape of the graph. This allowed memory usage and rendering cost to remain predictable even during long-running sessions.

---

# Zoom and Pan

This is the most interesting part of the project.

Your explanation is already good.

I would phrase it like this:

> The chart only contained a summarized view of the data. When a user zoomed into a specific time range, we requested higher resolution data from the server for that range. We also fetched additional data before and after the selected range as a buffer so that users could pan smoothly without triggering a server request on every movement.

Example:

```text
User Zooms

5s -------- 10s
```

Request:

```text
3s -------- 12s
```

Buffer:

```text
|--2s--| 5s--10s |--2s--|
```

---

# Very Likely Follow-up

### Why not keep all data on client?

Answer:

> At 500+ points per second over many hours, the dataset would become extremely large. Most of that data was not visible to the user at any given time, so storing and rendering everything on the client would increase memory consumption and reduce rendering performance. Instead we treated the server as the source of truth and fetched detailed data on demand during zoom and pan operations.

---

If I were interviewing you, the answer that would make me think "this guy has actually worked on real-time systems" is:

> React wasn't responsible for rendering every data point. MobX handled the streaming data, Chart.js handled canvas rendering, and React only managed the UI state. We also used a sliding window with on-demand data fetching for zoom and pan to keep memory and rendering costs bounded.

That's a much stronger answer than simply saying "we used Chart.js and optimized it."
