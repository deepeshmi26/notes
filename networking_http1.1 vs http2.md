# WIP

## What you will learn while reading this
- http/1.1 vs http/2
- Multiplexing  
- Head-of-line blocking  
- Header compression  
- Binary frames  

---

### TLDR;
```
HTTP/1.1 → multiple connections, sequential requests
HTTP/2 → one connection, many parallel streams, reusable headers
```

# Multi-lane highway analogy

## 1. HTTP/1.1 behaves like a single-lane road

HTTP/1.1 connections work like a **single-lane road**: only one request can move through the connection at a time.  
One request must complete before the next one progresses on the same connection.

Therefore, if multiple requests need to be made, the browser opens **multiple TCP connections**.

Example:

```
Connection 1 → request A → response A
Connection 2 → request B → response B
Connection 3 → request C → response C
```

### Problems with this approach

- Too many connections  
- Latency overhead due to repeatedly opening connections  
- Head-of-line blocking (one slow response blocks others)

### Practical problem

Modern websites load many resources simultaneously (JS, CSS, images, APIs).  
With HTTP/1.1, some of these requests can get **blocked or delayed**.

---

## 2. HTTP/2 introduced multiplexing

HTTP/2 solves this by turning the connection into a **multi-lane highway** instead of a single lane.

Instead of opening multiple connections, **one connection can carry many requests simultaneously**.

Example:

```
Single connection
├─ request A
├─ request B
├─ request C
```

This capability is called **multiplexing**.

Instead of opening many TCP connections, the browser can create **multiple logical streams inside one connection**.

```
Browser
   ↓
1 TCP connection
   ↓
many logical streams
```

Benefits:

- Fewer TCP connections
- Lower latency (since connection setup happens once)
- Multiple requests can progress simultaneously
- Helps reduce head-of-line blocking at the HTTP layer

---

# Binary parsing – smaller data that machines can process faster

## HTTP/1.1

HTTP/1.1 is **text-based**.

Example request:

```
GET /users HTTP/1.1
Host: api.example.com
User-Agent: Chrome
Accept: application/json
```

Example response:

```
HTTP/1.1 200 OK
Content-Type: application/json

{"name":"Deepesh"}
```

Everything is plain text and must be parsed as text.

---

## HTTP/2

HTTP/2 is **binary-based**.

Instead of sending text lines, HTTP/2 sends **frames**.

Conceptually:

```
[FRAME HEADER][DATA]
[FRAME HEADER][DATA]
[FRAME HEADER][DATA]
```

Machines parse binary faster than text, resulting in:

- Faster parsing
- Smaller data representation
- More efficient communication

---

# Header compression

In HTTP/1.1, request headers were sent **repeatedly with every request**.

Example headers:

- `Cookie`
- `User-Agent`
- `Authorization`
- `Accept`

These headers often remain the same across multiple requests.

### HTTP/2 solution

HTTP/2 introduces **header compression using HPACK**.

Instead of sending full headers every time, it sends only the **differences** after the first request.

Example:

```
Request 1 → full headers
Request 2 → only differences
Request 3 → only differences
```

Benefits:

- Reduced bandwidth usage
- Faster request transmission
