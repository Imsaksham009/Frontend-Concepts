Your actual answer is better than the generic "we used batching" answer because it's based on what your system really did.

I'd refine it slightly.

---

## How did you handle 500+ updates per second?

Don't say:

> We got 500 updates per second.

Say:

> Each WebSocket message could contain hundreds of data points. Instead of continuously accumulating all incoming data on the client, we treated the server as the source of truth. For most views, we replaced the existing dataset with the latest dataset received from the server rather than endlessly appending data. This prevented unbounded memory growth and kept rendering costs predictable.

Then mention chart-specific optimization:

> For charts, we also maintained a bounded window of data points. Once the visible dataset reached a predefined limit, older points were removed while preserving the visual representation of the chart.

This sounds much more architectural.

---

## Did every WebSocket message trigger a React render?

Your answer is correct but needs to be more precise.

I would say:

> No. Rendering was driven by the type of data received and the UI component that depended on it.
>
> Each WebSocket message contained a message type. Based on that type, only the relevant part of the application was updated.
>
> For example:
>
> * Packet messages updated the packet table.
> * Chart messages updated the chart dataset.
> * Status or metadata messages updated only their corresponding UI sections.
>
> We avoided triggering full application re-renders for every incoming message.

---

## Strong follow-up answer

Interviewer:

> So every packet message caused the entire table to re-render?

Answer:

> No. The update was scoped to the table module. Additionally, the table itself was virtualized, so even when new packet data arrived, only the visible rows were rendered in the DOM rather than the entire dataset.

---

## Another follow-up

Interviewer:

> Why replace old data instead of accumulating it?

Answer:

> Because the incoming stream was continuous and could run for many hours. If we continuously accumulated all incoming data on the client, memory consumption would grow indefinitely. Historical data remained available on the server and could be requested on demand when the user zoomed or navigated to a specific time range.

That's actually a very strong real-time systems answer.

---

One thing I'd avoid saying:

> First discard old data and then update new data.

An interviewer may think you're losing data.

Instead say:

> We maintained only the data necessary for the current visualization and treated the backend as the source of truth. Historical data could be fetched on demand when needed.

That sounds much more intentional and scalable.
