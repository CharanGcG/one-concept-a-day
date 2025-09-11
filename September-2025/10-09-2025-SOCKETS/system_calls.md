# 🔹 Lifecycle of a TCP Socket Connection (with System Calls)

We’ll split it into **Server side** and **Client side** and look at what happens:

---

## 1. **Connection Creation (3-Way Handshake)**

### 🖥️ Server Side

1. **`socket()`** → create a new socket descriptor.
2. **`bind()`** → attach it to `(server_ip, port)`.
3. **`listen()`** → mark it as a passive socket, ready for connections.
4. **`accept()`** → block and wait for a client.

   * When a client tries to connect, the OS completes the **3-way handshake**.
   * On success, `accept()` returns a **new socket descriptor** for that specific client (the original stays listening).

### 💻 Client Side

1. **`socket()`** → create a socket descriptor.
2. **`connect(server_ip, port)`** → initiate connection.

   * This triggers the **3-way handshake** (SYN → SYN+ACK → ACK).
   * When `connect()` returns, the connection is established.

---

## 2. **Data Transmission**

Once connected, both sides can send/receive.

### Common system calls:

* **`send()` / `write()`** → send bytes to the socket.

  * Data is copied into the kernel buffer and sent as TCP segments.
* **`recv()` / `read()`** → read bytes from the socket.

  * Blocks until data arrives (unless non-blocking or async).

### Behind the scenes:

* TCP ensures reliability (acknowledgements, retransmissions, ordering).
* OS kernel manages socket buffers, flow control, congestion control.

---

## 3. **Connection Termination (4-Way Handshake)**

Either side (client or server) can start closing. Let’s assume **client initiates**:

### 💻 Client Side

1. **`close()`** (or `shutdown(SHUT_WR)`) → sends **FIN** packet.
2. Enters **FIN\_WAIT\_1** state.
3. Receives ACK from server → enters **FIN\_WAIT\_2**.
4. Receives FIN from server → sends ACK, enters **TIME\_WAIT** (to catch stray packets).
5. After timeout (2×MSL), connection fully closes.

### 🖥️ Server Side

1. Receives client FIN → moves to **CLOSE\_WAIT**.
2. Application calls **`close()`** → sends FIN to client.
3. Moves to **LAST\_ACK**, waits for client’s final ACK.
4. Once received → connection fully closes.

---

# 🔹 Summary of Key System Calls

### **Server Side**

```
socket()
bind()
listen()
accept()
recv() / read()
send() / write()
close()
```

### **Client Side**

```
socket()
connect()
send() / write()
recv() / read()
close()
```

---

# 🔹 Timeline (Simplified)

```
Server: socket → bind → listen → accept → [recv/send] → close
Client: socket → connect → [send/recv] → close
```

With kernel doing:

* SYN / SYN+ACK / ACK (handshake)
* FIN / ACK / FIN / ACK (termination)

---

✅ **In essence:**

* **Creation** → `socket`, `bind`, `listen`, `accept` (server) / `socket`, `connect` (client)
* **Transmission** → `send`, `recv`
* **Termination** → `close`

---
