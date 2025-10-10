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

---
