

How did you reduce memory from 2GB to 500MB?

I would expect them to drill into:

How you discovered issue
Which profiler used
What caused rerenders
Why refs helped
Why selectors mattered



This is actually a strong story. The main thing is to make it sound like an engineer who systematically diagnosed a production problem rather than someone who randomly added `React.memo`.

Right now your explanation mixes multiple optimizations together. In an interview, structure matters.

I would tell it like this:

---

### Situation

"We had a React application that displayed large real-time charts using Chart.js. The application continuously polled a REST API and each poll could return around 1000 new data points. Originally the system was designed for smaller datasets and sessions lasting only a few hours, but over time users started running it for 24+ hours with significantly larger datasets.

As a result, memory consumption gradually increased to around 2GB and the application became sluggish."

---

### Investigation

"The first step was identifying where the memory growth was coming from.

I used React Profiler to analyze component render frequency and render duration. The profiler showed that the chart component was re-rendering on every polling cycle.

That immediately became suspicious because a chart receiving new data does not necessarily require the entire React component tree and canvas to be recreated."

---

### Root Cause

"After tracing the component hierarchy and props flow, I found that every polling response produced a new dataset object which caused React to re-render the chart component.

The legacy implementation recreated the entire Chart.js chart whenever new data arrived.

So instead of updating only the changed data points, we were repeatedly rebuilding a very large chart instance and canvas rendering pipeline."

---

### How I Fixed It

"There were multiple improvements.

First, I went through the Chart.js documentation and found that Chart.js provides APIs to update existing datasets rather than recreating the entire chart instance.

Instead of:

```text
New Poll
→ New Dataset
→ Destroy/Recreate Chart
→ Full Canvas Render
```

we changed it to:

```text
New Poll
→ Update Existing Dataset
→ Chart.update()
→ Incremental Render
```

This dramatically reduced rendering overhead."

---

### Additional Optimizations

"Once the main bottleneck was fixed, I profiled the application again and found other unnecessary renders.

For expensive components:

* React.memo was used to prevent renders when props had not changed.
* useMemo was used for expensive derived calculations.
* Some frequently changing data that did not affect UI rendering was moved into refs instead of state to avoid triggering React render cycles.

We also optimized chart rendering by reducing the number of visible points while preserving the visual representation of the data."

---

### Result

"After these changes, memory usage dropped from roughly 2GB to around 500MB during long-running sessions.

The application remained responsive even when processing very large datasets over extended periods."

---

Now let's prepare for the follow-up questions because this is where interviewers usually go.

### Why did React.memo help?

Good answer:

"Some parent components were re-rendering whenever polling data arrived. Even though certain child components received identical props, React would still invoke their render function. React.memo allowed us to skip those renders when props were unchanged."

---

### Why did useMemo help?

Good answer:

"Some chart-related calculations and data transformations were expensive. Without memoization they ran on every render. useMemo ensured those computations only ran when their dependencies actually changed."

---

### Why did refs help?

Be careful here.

Don't say:

> refs reduced memory usage.

That's not really the main reason.

Say:

"Some incoming streaming data was being stored in React state even though changes did not need to immediately update the UI. Because state updates trigger React's render cycle, we moved certain mutable values into refs. This allowed us to keep the latest data available without causing unnecessary re-renders."

---

### How exactly did React Profiler help?

Strong answer:

"React Profiler helped identify components with high render counts and long render durations. The chart component stood out because it was rendering on every polling cycle, which led us to investigate its implementation."

---

### Why was memory increasing over time?

This is a very likely question.

Answer:

"Because large datasets were continuously being recreated and rendered. Over long-running sessions, repeated allocations combined with expensive chart reinitialization increased memory pressure. Once we switched to updating existing chart instances and reduced unnecessary renders, memory stabilized."

---

This story is good because it demonstrates:

* Performance debugging
* React Profiler usage
* Understanding of React rendering
* Understanding of Chart.js internals
* Production troubleshooting
* Data-heavy frontend systems

Those are exactly the skills a Frontend Support Engineer would be expected to have.



### Q: How did you avoid memory leaks?

> Since our application could run continuously for 24+ hours while receiving hundreds of data points per second, preventing memory growth was a major concern.
>
> We focused on several areas:
>
> * We maintained a sliding window of chart data instead of storing unlimited points. Once the chart reached around 2000 points, older data was removed.
> * We avoided recreating Chart.js instances repeatedly. Instead of destroying and rebuilding charts on every update, we updated the existing dataset and called the chart update APIs.
> * We cleaned up polling timers, event listeners, and subscriptions whenever components unmounted.
> * We avoided keeping unnecessary historical data in React state and used MobX as the source of truth for streaming data.
> * We periodically profiled memory usage using Chrome DevTools and React Profiler to ensure memory stabilized during long-running sessions.
>
