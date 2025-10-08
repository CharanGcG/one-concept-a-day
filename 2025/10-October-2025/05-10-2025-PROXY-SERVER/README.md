# PROXY SERVER

## 1. What is a Proxy Server?

A **proxy server** is an intermediary between a client (like your computer or phone) and the internet (or another server).

* **Client → Proxy → Internet/Server**
* The client never communicates directly with the destination server; all requests go through the proxy first.

![Proxy server](/images/2025/October-2025/05-10-2025/proxy_server.webp)

---

## 2. Why Use a Proxy?

### a) **Privacy and Anonymity**

* Your real IP is hidden; the destination server only sees the proxy’s IP.
* Example: Accessing geo-restricted content.

### b) **Caching and Speed**

* Frequently requested data can be stored on the proxy.
* Reduces load times for clients and saves bandwidth.

### c) **Security**

* Proxies can filter malicious content, block websites, and prevent attacks.
* Often used in corporate networks to control internet usage.

### d) **Load Balancing**

* Some proxies distribute requests across multiple servers to improve performance.

---

## 3. Types of Proxy Servers

| Type                             | Function                                                  |
| -------------------------------- | --------------------------------------------------------- |
| **Forward Proxy**                | Sits between client and internet; hides client IP.        |
| **Reverse Proxy**                | Sits between internet and server; hides server IP.        |
| **Transparent Proxy**            | Client doesn’t know about it; used for caching/filtering. |
| **Anonymous Proxy**              | Hides client IP but reveals that it’s a proxy.            |
| **High Anonymity (Elite) Proxy** | Completely hides client IP and proxy usage.               |

![Forward and reverse proxy](/images/2025/October-2025/05-10-2025/0196-forward-proxy-vs-reverse-proxy.png)

---

## 4. How It Works (Step by Step)

1. Client makes a request to access a website.
2. The request goes to the proxy server first.
3. Proxy:

   * Checks its cache (if caching is enabled).
   * Filters the request (if security rules exist).
   * Forwards it to the destination server if needed.
4. The destination server responds to the proxy.
5. Proxy sends the response back to the client.

---

## 5. Developer Perspective

If you’re a **cloud or backend developer**, you might implement a proxy for:

* **Load Balancing:** Use reverse proxy servers like **NGINX** or **HAProxy**.
* **API Gateway:** Act as a single entry point to microservices.
* **Caching:** Reduce repeated data fetching with caching proxies like **Varnish**.
* **Security:** Block malicious requests or filter content.

---

### Quick Diagram

```
Client --> Forward Proxy --> Internet Server
Client <-- Forward Proxy <-- Internet Server

Internet Users --> Reverse Proxy --> Web Servers
Internet Users <-- Reverse Proxy <-- Web Servers
```

---
