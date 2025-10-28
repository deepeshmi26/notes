# HTTP, WebSocket, SSE, and HTTP Streaming — How They Build on TCP

> 📘 **Note:**  
> This file assumes you’ve already read the **[TCP vs UDP Overview](./TCP_vs_UDP.md)**.  
> That file explains the foundational transport layer behaviors these protocols rely on.

TLDR:
HTTP - Built on TCP to standardize request-resposne structure. 
Websocket - uses HTTP for connection and raw TCP for sending data. Bidirectional, expensive, CDN/proxy might not work.
HTTP streaming - Built on HTTP. Unidirectional, any response data type, parser logic required.
SSE - Built on HTTP streaming to standardize the response data type to be text, response buffered by CDN, supported mainly by browsers(not by apps)
---

When to choose which: 
1. Simple request-response - http(rest or graphql etc.)
2. Live unidirectional text response(json as text) - SSE
3. Live unidirectional + response data type is anything - http streaming
4. Bidirectional + one direction is more active that other(e.g. chatgpt) - http streaming/SSE + POST
5. Bidirectional + both directions are highly active - Websocket 

## 🌐 **1. HTTP (HyperText Transfer Protocol)**

- **Introduced:** 1991 by Tim Berners-Lee  
- **Built on:** **TCP**  
- **Purpose:** TCP allowed sending data, but the communication was not standardized. HTTP standardized request–response protocol for transferring web content.  
- **Used by:** Web pages, REST APIs, file downloads, browsers.

---

### **Flow**
1. **Client (browser)** opens a TCP connection.  
2. Sends an **HTTP request** (e.g., `GET /index.html`).  
3. **Server** processes and sends a **response** (headers + body).  
4. Connection may close (HTTP/1.0) or stay open for reuse (HTTP/1.1 Keep-Alive).

---

### **Technical details**
- **Stateless:** Each request is independent.  
- **Text-based:** Headers and body are human-readable.  
- **One-way:** Server responds only when requested.  
- **Persistent connections:** Optional reuse of the same TCP link.  

---

### **Impact on behavior**
- Simple, reliable, universal.  
- Cannot push data to client without a new request.  
- Each request–response adds at least one **TCP round-trip delay**.  

---

### **Use cases**
- REST APIs  
- Static web content  
- File uploads/downloads  

---

## 🔁 **2. WebSocket**

- **Introduced:** 2011 (RFC 6455)  
- **Built on:** **HTTP handshake + raw TCP**  
- **Purpose:** Full-duplex real-time communication.  
- **Used by:** Chat apps, multiplayer games, live trading dashboards.

---

### **Flow**
1. Client sends an **HTTP Upgrade** request:  
   `Upgrade: websocket`  
2. Server replies with `101 Switching Protocols`.  
3. Connection switches to **raw TCP**, both sides can send anytime.  

---

### **Technical details**
- **Full-duplex:** Both send and receive simultaneously.  
- **Persistent connection:** Stays open until closed.  
- **Framed messages:** Binary or text frames with small headers.  
- **Ping/Pong keep-alive:** Detects dropped connections.  

---

### **Impact on behavior**
- Low latency → no repeated handshakes.  
- Reliable → inherits TCP’s ordering and delivery.  
- Stateful → each open socket consumes memory, thus costs more money.
- Harder scaling → load balancers must maintain session (sticky sessions).  
- Gateways may block it → after switching to **raw TCP**, traffic no longer looks like HTTP.

---

### **Use cases**
- Real-time chat  
- Stock updates  
- Collaborative editors  
- Live dashboards  

---

## **3. HTTP Streaming (Chunked Transfer Streaming)**

- **Introduced:** HTTP/1.1 (1997)  
- **Built on:** **HTTP (TCP)**  
- **Purpose:** Continuous one-way streaming using the same HTTP connection.  
- **Used by:** AI token streaming (ChatGPT), incremental APIs, long responses.

---

### **Flow**
1. Client sends a normal HTTP request.  
2. Server responds with header:  
`Transfer-Encoding: chunked`.  
3. Server sends partial data as it’s generated:
      12
      {"token":"The"}
      
      10
      {"token":" sky"}
      
      0

4. Connection stays open until the stream is done.

---

### **Technical details**
- **No fixed format:** JSON, binary, or mixed.  
- **One-way stream:** Server pushes, client only listens.  
- **Manual flush control:** Server decides when to send chunks.  
- **Fully compatible with HTTP infra:** CDNs, load balancers, proxies.  

---

### **Impact on behavior**
- Lower latency → chunks flushed instantly.  
- Supports binary data (unlike SSE).  
- No auto-reconnect; client must handle restarts.  
- Easy to use outside browsers (Python, curl, Node, etc.).  
- Used where a strict SSE format would be restrictive.

---

### **Use cases**
- ChatGPT and AI token streams  
- Long-running analytics  
- Log tailing APIs
- 

## **3. SSE (Server-Sent Events) [A special case of HTTP streaming]**

- **Introduced:** 2009 (HTML5 spec)  
- **Built on:** **HTTP (TCP)**
- **Purpose:** HTTP streaming could send any data that had to be parsed. SSE standardized it to be text-based only.  
- **Used by:** Dashboards, notifications, live feeds.


---

### **Flow**
1. Client opens a connection via `EventSource("/stream")`.  
2. Server responds with header:  
   `Content-Type: text/event-stream`.  
3. Server sends messages like:
   `data: {"message":"hello"}
    data: {"message":"world"}
    data: [DONE]`
4. Connection remains open; browser reconnects automatically if dropped.

---

### **Technical details**
- **Unidirectional:** Server → client only.  
- **UTF-8 text only.**  
- **Auto-reconnect:** Browser handles disconnections transparently.  
- **Event framing:** Lines starting with `data:`.  

---

### **Impact on behavior**
- Lightweight and reliable (TCP-based).  
- Easy in browsers (built-in API).
- Might not be supported out of the browser - e.g., some mobile might not support sse for native apps
- No client→server channel; separate POSTs needed for replies.  
- Some proxies may buffer responses if not configured correctly.
  
---

### **Use cases**
- Stock tickers  
- Notification feeds  
- Live logs  

