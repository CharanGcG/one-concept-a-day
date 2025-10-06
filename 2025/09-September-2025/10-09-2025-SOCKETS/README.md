# 📘 Understanding Sockets in Computer Networks

---

## 🔹 What are Sockets?

* A **socket** is an endpoint for two-way communication between processes over a network.
* It is defined by a **socket address**:

  ```
  (IP Address, Port Number, Protocol)
  ```
* Think of a socket as a **doorway** for data:

  * **IP address** = Which house (machine).
  * **Port number** = Which door (application).
  * **Protocol** = How to knock (TCP or UDP).

---

## 🔹 Types of Sockets

1. **Stream Sockets (TCP)**

   * Connection-oriented, reliable, ordered.
   * Example: web browsing, email.

2. **Datagram Sockets (UDP)**

   * Connectionless, fast, but unreliable.
   * Example: gaming, video streaming.

3. **Raw Sockets**

   * Direct access to lower-level protocols.
   * Example: custom packet crafting, tools like `ping`.

---

## 🔹 How Does a Client Know the Server’s Socket?

* **Well-known ports** (HTTP → 80, HTTPS → 443, FTP → 21).
* **Configuration** (IP + port explicitly set).
* **DNS** (resolves domain name → IP address).
* **Service discovery** (registries, broadcasts in LANs).

---

## 🔹 How Servers Handle Multiple Clients

* A server binds to **one port** (e.g., 80).
* Each connection is identified by a unique **4-tuple**:

  ```
  (Client IP, Client Port, Server IP, Server Port)
  ```
* This allows thousands of clients to connect to the same server port simultaneously.
* The server maintains:

  * **Listening socket** → always on `(Server_IP, Server_Port)`.
  * **Connection sockets** → created per client after `accept()`.

---

## 🔹 Server Concurrency Models

1. **Fork per connection** → one process per client.
2. **Thread per connection** → one thread per client.
3. **Event-driven I/O** → one/few processes handling many sockets via `select()/epoll()`.
4. **Hybrid (modern servers)** → worker pools + event-driven.

---

## 🔹 Lifecycle of a TCP Socket Connection

![Socket system calls](/images/September-2025/10-09-2025/sockets.png)

### 1. **Connection Setup (3-Way Handshake)**

* **Client → Server**: SYN
* **Server → Client**: SYN+ACK
* **Client → Server**: ACK
  ✅ Connection established.

### 2. **Data Transmission**

* **`send()` / `write()`** → client/server send bytes.
* **`recv()` / `read()`** → receive bytes.
* OS kernel manages buffering, acknowledgements, retransmission.

### 3. **Connection Termination (4-Way Handshake)**

1. One side calls `close()` → sends **FIN**.
2. Other side ACKs → then sends its own FIN.
3. Initiator ACKs back → connection closed.

---

## 🔹 System Calls in Sequence

### Server Side

```c
socket()      // Create socket
bind()        // Bind to IP + port
listen()      // Mark socket as passive
accept()      // Wait for client, returns new socket
recv()        // Receive data
send()        // Send data
close()       // Close client socket
```

### Client Side

```c
socket()      // Create socket
connect()     // Connect to server (triggers 3-way handshake)
send()        // Send data
recv()        // Receive data
close()       // Close connection
```

---

## 🔹 Visual Flow

### Connection Setup

```
Client                                Server
socket()                              socket()
connect()  ---- SYN ------------->    bind(), listen()
           <--- SYN+ACK ----------    accept()
           ---- ACK -------------->   
```

### Data Transfer

```
send() --------------------------> recv()
recv() <-------------------------- send()
```

### Termination

```
Client (close) ---- FIN ---------> Server
               <--- ACK ----------
               <--- FIN ----------
           ---- ACK -------------> Connection closed
```

---

## ✅ Key Takeaways

* Sockets are the **endpoints** of communication.
* Servers use a **listening socket** and spawn **connection sockets** for each client.
* TCP uses a **3-way handshake** to start and a **4-way handshake** to close.
* System calls (`socket`, `bind`, `listen`, `accept`, `connect`, `send`, `recv`, `close`) form the backbone of socket programming.
* Concurrency is handled via **forking, threading, or event-driven models**.

---
