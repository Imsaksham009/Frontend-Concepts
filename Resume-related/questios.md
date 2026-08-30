# Interview Questions

## Q: Reason for change

GRL sold its services business to SGS, and I ended up in the entity that stayed behind. Since then the team has been restructured, and there's much less new product work — most of the projects are now in maintenance mode. I'm looking for a place where I can keep building and work on new things.

## Q: What is the most challenging task you have undertaken at work so far, and how did you handle it?

Our real-time protocol analysis app was climbing to 2–3 GB of memory when engineers ran it for long sessions, which broke their 24-hour test workflow. The hard part was diagnosis — the leak only surfaced after hours of running, so I couldn't reproduce it on demand.

I profiled with the React Profiler and compared heap snapshots taken at intervals to see what was being retained. It turned out the charts and tables were re-mounting and re-rendering on every batch of streaming data.

I fixed it in layers:

- Memoized the expensive components and scoped the selectors so one chart update didn't cascade into unrelated subtrees.
- Added virtualization to the tables and downsampling to the charts.
- Moved the high-frequency streaming data out of React state and into refs — it was changing 500 times a second, and almost none of those updates needed a render.

Memory came down to around 500 MB and stayed stable across a full 24-hour run.
