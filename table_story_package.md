Actually if you had used something like:

* `react-window`
* `react-virtualized`
* `TanStack Virtual`
* AG Grid virtualization

the story becomes easier to explain in interviews because most interviewers already know these libraries.

---

## System Design

Suppose:

```text
500 rows/minute
10 hours
```

Total rows:

```text
500 * 60 * 10
= 300,000 rows
```

No browser can smoothly render 300k DOM nodes.

---

## How it would work

```text
WebSocket
      ↓
Client Buffer
      ↓
Virtualization Library
      ↓
Visible DOM Rows Only
```

The virtualization library maintains:

```text
Total Row Count = 300000
Row Height = 24px
```

Virtual table height:

```text
300000 * 24px
= 7,200,000px
```

Huge scrollbar.

But only:

```text
20-50 rows
```

exist in DOM.

---

## What happens when new packets arrive

Every WebSocket packet:

```text
Packet #300001
Packet #300002
Packet #300003
```

gets appended into client memory.

Example:

```ts
rows.push(packet);
```

Then:

```ts
setRows([...rows]);
```

Virtualization library recalculates:

```text
Total Count = 300003
```

Scrollbar becomes slightly larger.

But DOM remains:

```text
~30 rows
```

No performance issue.

---

## What user sees

Suppose user is at bottom.

New packets arrive.

Virtualizer renders:

```text
299970
299971
299972
...
300000
```

As new rows arrive:

```text
299971
299972
...
300001
300002
```

If Auto Scroll is enabled:

```ts
scrollToBottom()
```

keeps user at latest data.

Exactly like a live log viewer.

---

## What if user scrolls to top?

This is where many candidates get confused.

We DON'T keep 300k rows in memory.

Usually we keep:

```text
Recent 500-2000 rows
```

in client.

Everything else stays on server.

---

### Example

Client currently has:

```text
299000 -> 300000
```

User suddenly scrolls upwards.

Virtualizer says:

```text
Need row 298500
```

Client doesn't have it.

Then:

```ts
GET /packets?start=298500&limit=500
```

Server returns:

```text
298500 -> 299000
```

These rows get inserted into local cache.

Virtualizer immediately renders them.

User feels like entire history exists.

---

## How scrolling works

Virtualizer continuously calculates:

```ts
visibleStartIndex
visibleEndIndex
```

Example:

```text
Scroll Position
      ↓
Row 100000
to
Row 100030
```

Only those rows are mounted.

Everything else is just calculated space.

---

## What if data keeps coming while user is reading old data?

This is a common interview question.

Answer:

"We separate data ingestion from viewport rendering."

Meaning:

```text
User Reading Row 1000
```

Meanwhile:

```text
Row 300001
Row 300002
Row 300003
```

keep arriving.

They are added to cache.

Scrollbar height increases.

But viewport stays where user is.

We do NOT force-scroll.

---

Only if:

```text
Auto Scroll = ON
```

then:

```ts
scrollToBottom()
```

keeps following new data.

---

### Interview Version (2-minute answer)

> We used a virtualization library so that regardless of whether there were 1,000 rows or 300,000 rows, only the visible rows were rendered in the DOM. Real-time packets arriving through WebSocket were appended to a client-side buffer and the virtualizer recalculated the total row count without increasing DOM size. The table height kept growing logically through the virtualizer, which maintained a large scrollbar while rendering only the rows currently visible. For historical data, we used lazy loading. When a user scrolled into a range not available in the local cache, the client fetched that batch from the server using row indexes. This allowed us to support continuous real-time updates and long-running sessions while keeping rendering performance stable.

This is very close to how AG Grid, TanStack Virtual, react-window, and most enterprise monitoring dashboards handle large real-time tables.


## Trickier Ques

Q: which Library i should mention. Also if user is reading the #1000 packet and WS is streaming #300000 packet onwards, here we will just keep on getting the total number of packets also. The total number of packets * row height will be the total height of the table so that scrollbar can keep on increasing.

ALso How we will handle if user suddenly scrolls. For example user is on #1000 packet and user suddenly scrolls to #200000 packet. So till there continous API calls will be made or what?



For this interview, I'd mention  **TanStack Virtual** .

Why?

* Modern React ecosystem
* They are already using TanStack Query in the JD
* Sounds current and relevant
* Easier to explain than react-virtualized
* More likely to impress than saying react-window

---

## Your understanding of total height is correct

Suppose:

```text
Packet Count = 300000
Row Height = 24px
```

Virtualizer internally thinks:

```text
Height = 300000 × 24
```

So scrollbar keeps growing.

When WS brings:

```text
300001
300002
300003
```

Packet count becomes:

```text
300003
```

Height becomes:

```text
300003 × 24
```

Scrollbar increases automatically.

Only visible rows are rendered.

---

# More interesting question

### User is reading packet #1000

Meanwhile WS is adding:

```text
300001
300002
300003
...
```

What happens?

Nothing.

Viewport remains around row #1000.

New packets are stored in cache.

Total height increases.

Scrollbar grows.

User doesn't get disturbed.

Only if Auto Scroll is enabled:

```ts
virtualizer.scrollToIndex(lastRow)
```

then viewport follows latest packets.

---

# The tricky part

### User suddenly jumps from #1000 to #200000

This is exactly what senior interviewers ask.

The answer is:

**No, we should never make 199,000 API calls.**

Bad approach:

```text
1000 -> 1500
1500 -> 2000
2000 -> 2500
...
```

Impossible.

---

## Real implementation

Server API should support range-based fetch.

Example:

```http
GET /packets?start=200000&limit=500
```

or

```http
GET /packets?rowIndex=200000
```

Server returns:

```text
200000 -> 200500
```

directly.

Single API call.

---

## How client knows what to fetch

Virtualizer tells:

```text
Visible Start Index = 200000
Visible End Index = 200030
```

Client checks cache.

```ts
if (!cache.has(200000)) {
   fetchRange(200000, 500);
}
```

Done.

---

## What about scrollbar dragging?

Suppose user drags scrollbar from top to middle.

Virtualizer calculates:

```text
scrollTop
      ↓
index = scrollTop / rowHeight
```

Example:

```text
scrollTop = 4,800,000px

index = 200000
```

Immediately.

No scrolling through intermediate rows.

No intermediate API calls.

Just:

```text
Need row 200000
```

↓

```text
Fetch row 200000
```

↓

```text
Render row 200000
```

---

## What answer sounds strongest in interview?

> We used virtualization with server-side lazy loading. The client only cached a small window of rows around the user's viewport. When the user jumped to a distant location, we calculated the target row index from the scroll position and requested that range directly from the server. We never loaded intermediate rows. This allowed us to support hundreds of thousands of packets while keeping memory usage and network traffic under control.

That's exactly how enterprise log viewers, monitoring dashboards, AG Grid Server-Side Row Model, and observability tools work.
