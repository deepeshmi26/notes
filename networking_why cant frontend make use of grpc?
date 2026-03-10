# Why browsers hide HTTP implementation behind intent APIs (like `fetch`)
This also answers the question "Why can you use GRPC in frontend"? Because GRPC makes use of HTTP/2 and even though browser makes use of http/2, browser hides http implementation behind intent apis(like `fetch`) 

## 1. Security boundary between web code and the network stack

Browsers run **untrusted JavaScript from any website**.  
The browser network layer must enforce security policies like:

- Same-origin policy  
- CORS  
- Mixed-content blocking (HTTP on HTTPS page)  
- Content Security Policy (CSP)

Example:

```
JS on evil.com tries to call api.bank.com
```

Browser checks:

```
Does api.bank.com allow this origin via CORS?
```

If JavaScript could control low-level HTTP behavior (streams, frames, connections), malicious sites could attempt to **bypass browser protections**.

So the rule is:

```
Untrusted JS → cannot control network transport
Trusted browser layer → enforces security
```

---

## 2. Browser must manage connections globally (connection pooling & scheduling)

A browser is not a single application. It runs **many websites simultaneously**.

Example:

```
Tab 1 → YouTube
Tab 2 → Gmail
Tab 3 → News site
```

The browser manages network resources such as:

- Reusing connections  
- Limiting number of connections  
- Prioritizing important requests  
- Closing idle connections  
- Sharing bandwidth across tabs  

Example problem if JS controlled HTTP/2 streams:

```
JS opens 200 HTTP/2 streams
keeps connection alive forever
sets highest priority
```

A single site could **hog the network and slow down other tabs**.

So the browser keeps control over:

```
connection reuse
stream creation
request priority
bandwidth scheduling
```

---

## 3. The web platform must survive protocol evolution

Internet protocols evolve over time.

Example evolution:

```
HTTP/1.1 → HTTP/2 → HTTP/3 (QUIC)
```

If browsers exposed APIs like:

```
createHTTP2Stream()
sendHTTP2Frame()
setStreamPriority()
```

Then when HTTP/3 arrived, these APIs would become obsolete and break websites.

Instead browsers expose **intent-based APIs**:

- `fetch()`
- `WebSocket`
- `WebRTC`

Example:

```
fetch("/users")
```

The browser decides internally whether to use:

```
HTTP/1.1
HTTP/2
HTTP/3
```

Websites continue working even as protocols evolve.

---

## 4. Prevent websites from turning HTTP into arbitrary transport tunnels

If JavaScript had HTTP/2 stream control, a site could run its **own custom protocol over HTTP**.

Example:

```
open persistent HTTP/2 stream
send custom binary messages
build chat/game/database protocol
```

Now the traffic becomes opaque to infrastructure like:

- CDNs  
- Proxies  
- Gateways  
- Security filters  

These systems expect normal HTTP semantics:

```
request → response
headers
content types
```

Allowing arbitrary protocols over HTTP streams would break assumptions used by internet infrastructure.

---

## One-line memory version

```
JS expresses intent (fetch data)
Browser controls transport (HTTP implementation)
```

This separation keeps the web:

- Secure  
- Fair across tabs  
- Future-proof to protocol changes  
- Compatible with internet infrastructure



## How does browse implement http2 ?

The web platform follows this rule:

```
JavaScript describes WHAT it wants
Browser decides HOW the network works
```

Example:

```
JS:
fetch("/users")

Browser:
reuse existing HTTP/2 connection
create stream
schedule request
send over network
```

---
