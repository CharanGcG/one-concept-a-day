# LOAD BALANCING

![Load Balancer](/images/2025/October-2025/04-10-2025/Load-balancing.png)

### **1. What is Load Balancing?**

Load balancing is the process of distributing incoming network traffic or workloads **across multiple servers** so that no single server is overwhelmed. It ensures **high availability, reliability, and better performance** of applications.

Think of it like a **traffic cop** directing cars to multiple lanes to avoid traffic jams.

---

![Load Balancing](/images/2025/October-2025/04-10-2025/Load-Balancer-multi-users.png)

### **2. Why is it Needed?**

* Prevents a single server from getting overloaded.
* Improves **response time** for users.
* Ensures **redundancy**—if one server fails, traffic can go to others.
* Helps scale applications horizontally by adding more servers.

---

### **3. How It Works**

Imagine you have a website with three servers: `S1`, `S2`, `S3`. A **Load Balancer (LB)** sits in front of them.

```
      Incoming Requests
             |
          [ Load Balancer ]
           /      |      \
         S1       S2      S3
```

The load balancer decides which server should handle each request, based on the chosen algorithm.

---

![Load balancer](/images/2025/October-2025/04-10-2025/How-Load-Balancer-works.webp)

### **4. Types of Load Balancing**

**A. Based on Traffic Layer:**

1. **Layer 4 (Transport Layer) Load Balancing**

   * Works on IP address and TCP/UDP ports.
   * Fast and simple.
   * Doesn’t inspect content of requests.

2. **Layer 7 (Application Layer) Load Balancing**

   * Works on HTTP/HTTPS requests.
   * Can route based on URLs, headers, cookies, etc.
   * More intelligent and flexible.

**B. Based on Algorithms:**

1. **Round Robin** – Sends requests sequentially to each server.
2. **Least Connections** – Sends traffic to the server with the fewest active connections.
3. **IP Hash** – Uses client IP to decide which server gets the request.
4. **Weighted Round Robin** – Servers get traffic proportionally to their capacity.

---

### **5. Types of Load Balancers**

1. **Hardware Load Balancers** – Physical devices, very fast, expensive.
2. **Software Load Balancers** – Run on normal servers, flexible (e.g., Nginx, HAProxy).
3. **Cloud/Managed Load Balancers** – Provided by cloud platforms (e.g., AWS ELB, GCP Load Balancer, Azure LB).

---

### **6. Real-Life Analogy**

Imagine a busy restaurant:

* You have 3 chefs (servers) and a host (load balancer).
* If all customers rush to one chef, meals are delayed.
* The host assigns each new customer to the chef who can handle them fastest.
* Even if one chef leaves, the host redirects customers to the remaining chefs.

---

### **7. Key Benefits**

* **Scalability** – Add more servers without downtime.
* **High Availability** – No single point of failure.
* **Better Performance** – Reduced latency, faster response.
* **Flexibility** – Can do SSL termination, caching, content switching.

---

Let’s look at **how a developer implements load balancing** in real-world systems.


## **1. Software Load Balancing (For Developers)**

If you’re running your own servers (or microservices), the most common approach is using a **software load balancer** like **Nginx, HAProxy, or Traefik**.

### **A. Using Nginx as a Load Balancer**

Suppose you have 3 backend servers running your app on ports 5001, 5002, 5003.

**Nginx config (simplified)**:

```nginx
http {
    upstream my_app {
        server 127.0.0.1:5001;
        server 127.0.0.1:5002;
        server 127.0.0.1:5003;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://my_app;
        }
    }
}
```

✅ This will **distribute requests** among the three backend servers.

---

### **B. Using HAProxy**

HAProxy is another popular option:

```haproxy
frontend http_front
    bind *:80
    default_backend servers

backend servers
    balance roundrobin
    server s1 127.0.0.1:5001 check
    server s2 127.0.0.1:5002 check
    server s3 127.0.0.1:5003 check
```

Here, HAProxy checks **server health** and sends traffic using **Round Robin** by default.

---

## **2. Load Balancing in Microservices**

If you’re using **Docker or Kubernetes**:

* **Kubernetes:** Uses **Services** with type `LoadBalancer` or `Ingress`:

  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: my-app-service
  spec:
    type: LoadBalancer
    selector:
      app: my-app
    ports:
      - protocol: TCP
        port: 80
        targetPort: 5000
  ```

  * Kubernetes handles distributing requests to Pods automatically.
  * Can use **Ingress controllers** (like Nginx Ingress) for L7 load balancing.

* **Docker Swarm:** Has built-in **routing mesh** that distributes incoming traffic among service replicas.

---

## **3. Cloud Load Balancers**

Most developers now rely on cloud providers for production:

| Provider | Load Balancer Type              | Features                                     |
| -------- | ------------------------------- | -------------------------------------------- |
| AWS      | Elastic Load Balancer (ALB/NLB) | HTTP(S) & TCP, auto-scaling, SSL termination |
| GCP      | Cloud Load Balancing            | Global, HTTP(S), TCP/UDP                     |
| Azure    | Azure Load Balancer             | Layer 4 & 7, integrates with VM Scale Sets   |

✅ Steps as a developer:

1. Deploy multiple instances of your app.
2. Create a load balancer pointing to these instances.
3. Optionally configure **health checks, sticky sessions, SSL termination**.
4. Cloud LB automatically routes traffic based on rules.

---

## **4. Developer Tips / Best Practices**

* **Use health checks**: Don’t send traffic to unhealthy servers.
* **Use sticky sessions if needed**: Some apps need the same user to hit the same server.
* **Start with round robin**: Simple and often sufficient.
* **Scale horizontally**: Add more servers/pods when traffic grows.
* **Monitor metrics**: CPU, memory, request latency to adjust traffic routing.

---

