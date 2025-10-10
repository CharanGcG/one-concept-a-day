# API GATEWAY

### 🧩 **Definition**

An **API Gateway** is a **single entry point** for all client requests that go to multiple backend services.
It handles:

* Request routing
* Load balancing
* Authentication
* Rate limiting
* Logging
* And sometimes caching

You can think of it as a **traffic controller** for your APIs.

![gateway](/images/2025/October-2025/07-10-2025/gateway-text-diagram.png)

---

### ⚙️ **Architecture Overview**

```
        ┌───────────────┐
        │     Client    │
        └──────┬────────┘
               │
               ▼
        ┌────────────────────┐
        │    API GATEWAY     │
        │ - Auth             │
        │ - Rate limit       │
        │ - Routing          │
        │ - Caching          │
        └──────┬─────────────┘
               │
 ┌─────────────┼───────────────────────┐
 │             │                       │
 ▼             ▼                       ▼
User Service  Order Service       Payment Service
```

![ms gateway](/images/2025/October-2025/07-10-2025/ms-gateway.png)

---

### 🧠 **Why use an API Gateway?**

1. **Centralized control** – one place for security, logging, and throttling.
2. **Hides complexity** – clients don’t need to know about all backend services.
3. **Version management** – easier to handle API versioning.
4. **Reduces cross-cutting duplication** – things like auth or logging are implemented once.

---

### 🔧 **Common Implementations**

* **AWS API Gateway**
* **Kong Gateway**
* **NGINX**
* **Traefik**
* **Apigee**
* **Express Gateway** (Node.js-based)

---

### 👨‍💻 Example (Simplified Java / Spring Boot)

```java
@SpringBootApplication
public class ApiGatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(ApiGatewayApplication.class, args);
    }
}

@Configuration
public class GatewayConfig {
    @Bean
    public RouteLocator customRoutes(RouteLocatorBuilder builder) {
        return builder.routes()
            .route("user_route", r -> r.path("/user/**")
                .uri("http://localhost:8081"))
            .route("order_route", r -> r.path("/order/**")
                .uri("http://localhost:8082"))
            .build();
    }
}
```

🧩 Here, the gateway routes:

* `/user/*` → User Service (port 8081)
* `/order/*` → Order Service (port 8082)

---

### 🚀 **Flow Example**

```
Client → /order/checkout
↓
API Gateway → validates token → rate limits → routes request
↓
Order Service → processes → returns response
↓
Gateway → sends back to client
```


![aws gateway](/images/2025/October-2025/07-10-2025/39_api_gateway_5_33791ea78a.png)

---

## 🧭 Step-by-step: What happens when you click a button?

Let’s say you’re using an app like this:

👉 You click a button: **“Place Order”**
This triggers a frontend API call:

```js
POST https://api.mystore.com/order
```

Now let’s trace what happens.

---

### 🧩 **1. Frontend sends the request**

Your browser or app makes an HTTPS request.
It doesn’t directly know which backend service (Order Service, Payment Service, etc.) should handle it —
it just calls the **API Gateway endpoint**:

```
https://api.mystore.com/order
```

---

### ⚙️ **2. API Gateway receives it first**

The **API Gateway** is like a receptionist in a large office:

* It **authenticates** you (checks your token or session).
* It **validates the request** (e.g., body, rate limit, etc.).
* It **logs** the call for monitoring.

Then it looks at its routing configuration — something like:

| Path pattern | Target service                                             |
| ------------ | ---------------------------------------------------------- |
| /user/**     | [http://user-service:8081](http://user-service:8081)       |
| /order/**    | [http://order-service:8082](http://order-service:8082)     |
| /payment/**  | [http://payment-service:8083](http://payment-service:8083) |

It matches `/order` → routes to **Order Service**.

---

### 🔁 **3. Gateway forwards the request**

The API Gateway **forwards** the request to the backend service (Order Service).
It might also:

* Add headers (like user info or trace ID)
* Transform the request (e.g., change JSON format or endpoint)
* Do load balancing between multiple Order Service instances

```
API Gateway → http://order-service:8082/order
```

---

### 💾 **4. Order Service processes it**

The **Order Service** now executes the logic — creates an order in DB, verifies stock, etc.
It then sends the response back to the Gateway:

```
{ "message": "Order placed successfully", "orderId": 1201 }
```

---

### 🚀 **5. API Gateway sends response to frontend**

The Gateway might:

* Cache or compress the response
* Strip out internal headers
* Log the response time

Then it returns it to the frontend app (the one that made the click).

So, the complete flow:

```
You (Browser)
     ↓
[ Frontend JS API call ]
     ↓
🌐 API Gateway (security, routing, load balancing)
     ↓
🎯 Backend microservice (User/Order/Payment)
     ↓
🌐 API Gateway (response processing)
     ↓
You get the response on UI
```

---

### 🧠 **Key Idea**

➡️ The client never directly talks to microservices.
➡️ The API Gateway hides all backend complexity.
➡️ It acts as a *reverse proxy + controller + policy enforcer*.

---

