WebSockets is actually where you can stand out because your resume has  **real production experience** , not toy chat-app examples.

If I were interviewing you, these are the questions I'd ask.

---

# Level 1: Fundamentals

### What is WebSocket?

Expected:

> WebSocket is a full-duplex communication protocol that allows persistent bidirectional communication between client and server over a single TCP connection.

---

### WebSocket vs HTTP

| HTTP                       | WebSocket         |
| -------------------------- | ----------------- |
| Request/Response           | Bidirectional     |
| Stateless                  | Persistent        |
| New connection per request | Single connection |
| Polling required           | Push based        |

---

### WebSocket vs Polling

This is guaranteed.

Answer:

> Polling continuously asks the server for updates even when nothing changes. WebSocket allows the server to push updates immediately when data changes.

---

### WebSocket vs SSE

Good interview question.

SSE:

```text
Server → Client
```

WebSocket:

```text
Server ↔ Client
```

---

# Level 2: Architecture

### Explain your WebSocket architecture.

You should prepare:

```text
Backend Producer
       ↓
WebSocket Server
       ↓
Browser Client
       ↓
MobX Store
       ↓
Chart/Table
```

---

### How were messages structured?

Example:

```json
{
  "type":"packet",
  "rowIndex":1234,
  "timestamp":123456
}
```

---

### How did you subscribe to updates?

Expect code-level discussion.


> We didn't have a single strategy for all data. The optimization depended on the component consuming the stream. For charts, we used bounded datasets and Chart.js update APIs. For tables, we used virtualization and lazy loading. In general, we avoided unbounded client-side accumulation and ensured only the data required for the current view was rendered.



Usually the interviewer is asking:

> How did frontend know that new WebSocket data arrived?

A good answer would be:

> We maintained a single WebSocket connection when the user started a session. Different modules in the application subscribed to specific message types. Whenever a message arrived, we parsed the payload type and routed it to the appropriate consumer such as the chart module, packet table, or status panel.

Example:

<pre class="overflow-visible! px-0!" data-start="492" data-end="735"><div class="relative w-full mt-4 mb-1"><div class=""><div class="contents"><div class="relative"><div class="h-full min-h-0 min-w-0"><div class="h-full min-h-0 min-w-0"><div class="border border-token-border-light border-radius-3xl corner-superellipse/1.1 rounded-3xl"><div class="h-full w-full border-radius-3xl bg-token-bg-elevated-secondary corner-superellipse/1.1 overflow-clip rounded-3xl lxnfua_clipPathFallback"><div class="pointer-events-none absolute end-1.5 top-1 z-2 md:end-2 md:top-1"></div><div class="relative"><div class="pe-11 pt-3"><div class="relative z-0 flex max-w-full"><div id="code-block-viewer" dir="ltr" class="q9tKkq_viewer cm-editor z-10 light:cm-light dark:cm-light flex h-full w-full flex-col items-stretch ͼs ͼ16"><div class="cm-scroller"><pre class="cm-content q9tKkq_readonly m-0"><code><span>WebSocket Message</span><br/><span>        ↓</span><br/><span>Message Router</span><br/><span>        ↓</span><br/><span>+------------+-----------+-----------+</span><br/><span>| Packet     | Chart     | Status    |</span><br/><span>+------------+-----------+-----------+</span><br/><span>        ↓          ↓          ↓</span><br/><span>     Table      Chart      UI</span></code></pre></div></div></div></div></div></div></div></div></div><div class=""><div class=""></div></div></div></div></div></div></pre>

---

If they ask  **code-level** , you can say:

<pre class="overflow-visible! px-0!" data-start="784" data-end="1128"><div class="relative w-full mt-4 mb-1"><div class=""><div class="contents"><div class="relative"><div class="h-full min-h-0 min-w-0"><div class="h-full min-h-0 min-w-0"><div class="border border-token-border-light border-radius-3xl corner-superellipse/1.1 rounded-3xl"><div class="h-full w-full border-radius-3xl bg-token-bg-elevated-secondary corner-superellipse/1.1 overflow-clip rounded-3xl lxnfua_clipPathFallback"><div class="pointer-events-none absolute inset-x-4 top-12 bottom-4"><div class="pointer-events-none sticky z-40 shrink-0 z-1!"><div class="sticky bg-token-border-light"></div></div></div><div class="relative"><div class=""><div class="relative z-0 flex max-w-full"><div id="code-block-viewer" dir="ltr" class="q9tKkq_viewer cm-editor z-10 light:cm-light dark:cm-light flex h-full w-full flex-col items-stretch ͼs ͼ16"><div class="cm-scroller"><pre class="cm-content q9tKkq_readonly m-0"><code><span class="ͼ11">socket</span><span class="ͼv">.</span><span>onmessage </span><span class="ͼv">=</span><span> (</span><span class="ͼ11">event</span><span>) => {</span><br/><span></span><span class="ͼv">const</span><span></span><span class="ͼ11">message</span><span></span><span class="ͼv">=</span><span></span><span class="ͼ11">JSON</span><span class="ͼv">.</span><span>parse(</span><span class="ͼ11">event</span><span class="ͼv">.</span><span>data);</span><br/><br/><span></span><span class="ͼv">switch</span><span>(</span><span class="ͼ11">message</span><span class="ͼv">.</span><span>type) {</span><br/><span></span><span class="ͼv">case</span><span></span><span class="ͼz">'packet'</span><span>:</span><br/><span></span><span class="ͼ11">packetStore</span><span class="ͼv">.</span><span>update(</span><span class="ͼ11">message</span><span>);</span><br/><span></span><span class="ͼv">break</span><span>;</span><br/><br/><span></span><span class="ͼv">case</span><span></span><span class="ͼz">'chart'</span><span>:</span><br/><span></span><span class="ͼ11">chartStore</span><span class="ͼv">.</span><span>update(</span><span class="ͼ11">message</span><span>);</span><br/><span></span><span class="ͼv">break</span><span>;</span><br/><br/><span></span><span class="ͼv">case</span><span></span><span class="ͼz">'status'</span><span>:</span><br/><span></span><span class="ͼ11">statusStore</span><span class="ͼv">.</span><span>update(</span><span class="ͼ11">message</span><span>);</span><br/><span></span><span class="ͼv">break</span><span>;</span><br/><span>   }</span><br/><span>};</span></code></pre></div></div></div></div></div></div></div></div></div><div class=""><div class=""></div></div></div></div></div></div></pre>


### Follow-up: What happened when component unmounted?

Answer:

> We cleaned up subscriptions and observers during component unmount. This prevented stale listeners and memory leaks in long-running sessions.

---

For your GRL-C3 project, I'd answer:

> We maintained a centralized WebSocket connection. Incoming messages contained a type field, and based on that type we routed the data to the appropriate MobX store. React components observed only the stores relevant to them, so a chart update didn't trigger updates in the packet table and vice versa.

---

# Level 3: Real-Time Data Handling

These are the interesting questions.

---

### You receive 500 rows/sec.

How do you avoid UI freezing?

Expected answer:

* Batch updates
* Buffer updates
* Avoid render per message
* Virtualization
* Chart update APIs

---

### What if messages arrive faster than rendering?

Good answer:

```text
Incoming Queue
      ↓
Batch Processing
      ↓
UI Update
```

---

### Did every WebSocket message trigger React render?

They'll love this question.

Correct answer:

> No. We buffered updates and avoided React renders for every incoming message.



> No, every WebSocket message did not trigger a full React render.
>
> Incoming WebSocket messages contained a type field, and based on that type we routed the data to the appropriate store or module. For example, chart-related messages updated the chart data, packet messages updated the packet table, and status messages updated their corresponding UI sections.
>
> Because of this separation, only the components consuming that particular data were updated. A chart update would not cause the packet table to re-render and vice versa.
>
> We also paid attention to component boundaries and memoization. If a parent component re-rendered, child components would only re-render when their props or observed state actually changed. We used techniques such as React.memo and memoized computations where appropriate to avoid unnecessary rendering.



One sentence that sounds particularly strong:

> We treated WebSocket messages as domain-specific updates rather than application-wide updates, which allowed us to keep rendering scoped to only the affected components.
>

### "But if a MobX store changes, won't React re-render?"

Answer:

> Only the observer components that depend on the changed observable state will re-render. Components that are not observing that piece of state remain unaffected.

---

# Level 4: Reliability

These are support engineer questions.

---

### What happens if socket disconnects?

Most likely question.

Possible answer:

```text
Connection Lost
      ↓
Reconnect
      ↓
Resubscribe
      ↓
Recover Missing Data
```

---

### How would you implement reconnection?

Answer:

```text
1s
2s
4s
8s
16s
```

Exponential backoff.

> Since our application was an Electron desktop application and both the client and WebSocket server were running on the same machine, a WebSocket disconnection was usually an indication that the backend service had stopped, crashed, or was restarting rather than a network issue.
>
> When a disconnection occurred, we attempted automatic reconnection using exponential backoff. For example, we retried after 1 second, then 2 seconds, then 4 seconds, and so on up to a maximum retry limit.
>
> If reconnection succeeded, the normal handshake process occurred again and the client re-established communication with the backend.
>
> If the maximum retry count was exceeded, we assumed the backend service was unavailable and informed the user that the application needed to be restarted.



### Why exponential backoff?

Expected follow-up.

Answer:

> We wanted to avoid continuously hammering the backend process if it was starting up, recovering, or had crashed. Exponential backoff reduces unnecessary connection attempts while still allowing quick recovery when the service becomes available again.



What data was lost during disconnect?

> Since the application was running locally and disconnections generally indicated a backend restart, our primary goal was to restore communication and refresh the latest state from the backend after reconnection.

---

### Why not reconnect every 100ms?

Answer:

Could overwhelm server.

---

### How do you know connection is alive?

Answer:

Heartbeat / Ping-Pong.

Example:

```text
Client Ping
Server Pong
```

### Concept

TCP/WebSocket connections can appear open even when the other side is dead.

Example:

```text
Client  ───────── Server

Server crashes
```

Client may not immediately know.

So we use  **heartbeats** .

## Heartbeat Mechanism

Every few seconds:

```text
Client ---> Ping
Server ---> Pong
```

or

```text
Server ---> Heartbeat
Client ---> Ack
```

If heartbeat is not received for a certain duration:

```text
Heartbeat Timeout
       ↓
Connection Dead
       ↓
Reconnect
```

## Typical Implementation

Client:

```ts
setInterval(() => {
   socket.send({
      type: "ping"
   });
}, 5000);
```

Server:

```ts
if(message.type === "ping") {
   socket.send({
      type: "pong"
   });
}
```

Client:

```ts
lastPongReceived = Date.now();
```

Then periodically:

```ts
if(Date.now() - lastPongReceived > 15000) {
    socket.close();
    reconnect();
}
```

Meaning:

```text
No heartbeat for 15 sec
      ↓
Connection considered dead
```

## For Your Project

Think carefully:

### Did you actually implement heartbeat?

Many systems don't.

Instead they rely on:

```text
onclose
onerror
```

events.

If that's what your project did, say that.

### Honest answer

If you did not implement heartbeat:

> In our application we primarily relied on WebSocket lifecycle events such as `onclose` and `onerror` to detect connection failures. Since the client and backend were running on the same machine inside an Electron application, most disconnects were caused by backend process failures rather than network instability.

### Better answer if heartbeat existed

> We maintained connection health through a heartbeat mechanism. The client periodically sent ping messages and expected pong responses from the backend. If heartbeat responses were not received within a configured timeout window, we treated the connection as dead and initiated our reconnection strategy.

### Follow-up

### Why not only rely on onclose?

Strong answer:

> Because a connection can become stale without immediately triggering an `onclose` event. Heartbeats allow us to proactively detect silent failures and recover faster.

That's the answer interviewers usually want to hear. Even if your project didn't implement it, you should know the concept because many real-time systems use heartbeat monitoring.

---

### What if user loses internet for 5 minutes?

Strong question.

Expected:

> After reconnecting, I would request missing data from the server based on timestamp or sequence number.

---

# Level 5: Performance

### Memory leak risks with WebSocket?

Answer:

* Unclosed sockets
* Unremoved listeners
* Growing queues
* Growing caches

---

### How would you debug WebSocket memory issues?

Answer:

* Heap snapshots
* Network tab
* Listener inspection
* Message queue growth

---

### Why not store all WebSocket data forever?

You already have a good answer:

> We used bounded buffers and fetched historical data on demand.

---

# Level 6: Production Debugging

These are very likely for a Support Engineer.

---

### User says real-time updates stopped.

What do you check?

Answer:

1. Browser console
2. WebSocket state

```js
socket.readyState
```

3. Network tab
4. Server logs
5. Reconnect logic

---

### Some users receive updates, others don't.

Possible causes:

* Subscription issue
* Permission issue
* Network issue
* Backend routing issue

---

### Data arrives out of order.

How would you handle it?

Good answer:

> Use sequence numbers or timestamps and sort before rendering.

---

### Duplicate messages arrive.

Answer:

> Use unique message IDs and deduplicate on client.

---

# Advanced Questions

These are the ones that impress interviewers.

---

### What is backpressure?

Suppose:

```text
Server sends 5000 msgs/sec
Client processes 500 msgs/sec
```

Queue grows forever.

This is backpressure.

If they ask:

> What is backpressure and did you face it?

You can answer:

> Backpressure occurs when data is produced faster than the consumer can process it. In our case, WebSocket streams could deliver large amounts of chart and packet data. If we continuously accumulated and rendered everything, memory usage and rendering latency would grow over time. We handled this by maintaining bounded datasets, replacing or trimming older data, virtualizing large tables, and fetching detailed historical data on demand instead of keeping everything in memory.
>

---

### How would you handle it?

Options:

* Batch messages
* Drop old messages
* Sampling
* Server throttling

---

### Why TCP if WebSocket already exists?

Expected:

> WebSocket is an application protocol that runs over TCP.

---

# Questions most likely from your resume

I'd prepare these first:

1. Why WebSocket instead of polling?
2. How did you handle 500+ updates/sec?
3. Did every message trigger React render?
4. How did you prevent memory growth?
5. What happens if socket disconnects?
6. How would you reconnect?
7. How would you recover missed data?
8. How did you handle historical data?
9. How would you debug stopped updates?
10. What if messages arrive faster than UI can process?

These are exactly the kinds of questions I'd expect from a team building React dashboards, monitoring tools, workflow systems, and real-time interfaces like the ones mentioned in the TresVista role.
