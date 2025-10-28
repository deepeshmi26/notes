
# Network Protocols Overview — TCP, UDP

---

## 1. Transport Layer Foundations

### **TCP (Transmission Control Protocol)**
- **Introduced:** 1974 (formalized in 1981, RFC 793)
- **Purpose:** Reliable, ordered, connection-oriented data delivery.
- **Used by:** HTTP, WebSocket, SSE, Email (SMTP), etc.

### **Flow**

#### **1. Connection setup — 3-way handshake**
1. Client → **SYN** → sends request to connect to server
2. Server → **SYN-ACK** → sends ready ack to client
3. Client → **ACK** → sends confirmation ack to server
→ Establishes a mutual agreement before data transfer (**connection-oriented**).  

---

#### **2. Data transfer**
- Both sides send data simultaneously (**full-duplex**).  
- Packets include **sequence numbers** for reordering.  
- Receiver sends **ACKs** for confirmation, only then next set of packets are transmitted  
- Missing packets are retransmitted.  
- If a packet is **not acknowledged within a timeout**, the sender **retransmits** it automatically.  
- Multiple lost packets trigger **congestion control**, slowing the send rate.  
→ Ensures **reliability** and **ordered delivery**.

---

#### **3. Flow & congestion control**
- **Flow control:** Receiver signals sender when buffers are full.  
- **Congestion control:** Sender reduces rate if network is crowded.  
→ Maintains stability and avoids packet loss.

---

#### **4. Connection teardown**
- Exchange of **FIN** and **ACK** packets closes the session.  
→ Ensures all data is received before disconnect.

---

**How this impacts features/drawbacks**
- **Reliable and ordered data** → perfect for text, files, API calls.
- **Extra control overhead** → adds latency.
- **Stateful connections** → each session consumes memory.

**Clarifying question discussed:**
> “Does TCP only send acknowledgements?”
→ No. Both sides send **data + acknowledgements**. TCP is **full-duplex** — both can send simultaneously.

---

### **UDP (User Datagram Protocol) [Read TCP before reading this]**
- **Introduced:** 1980 (RFC 768)
- **Purpose:** Lightweight, connectionless, low-latency communication.
- **Used by:** DNS, VoIP, live gaming, WebRTC, QUIC (HTTP/3).

**Technical details**
- **Connectionless:** No handshake or persistent state.
- **Unreliable:** No ACKs, no retransmission, no order guarantee.
- **Stateless:** Each packet independent.
- **Header size:** 8 bytes.
- **Supports broadcast/multicast.**

**How this impacts features**
- **Ultra-fast** → minimal latency.
- **Packet loss possible** → applications must handle reliability if needed.
- **Perfect for real-time apps** where timeliness > accuracy.
