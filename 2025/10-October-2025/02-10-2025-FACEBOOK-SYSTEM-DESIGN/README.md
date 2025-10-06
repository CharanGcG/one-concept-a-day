# Facebook — quick design brief

**Primary goal:** support a global social graph (people, posts, likes, relationships, media) with low-latency read/write, high availability, and evolving features (feed ranking, photos, messaging, live video, ads, analytics).
**Non-functional priorities:** extreme scale, low read latency, horizontal scalability, incremental deploys, operational visibility, cost-efficiency.

![High level design](/images/2025/October-2025/02-10-2025/High-Level-Design-facebook-design-new.webp)

![Low level design](/images/2025/October-2025/02-10-2025/Low-level-design-facebook-new.webp)

---

# Evolution timeline (conceptual)

1. **Single server → monolith (early days)**

   * Simple LAMP-like stack (PHP + MySQL). Fast iteration prioritized over scale.
2. **Scale-out with caching and sharding**

   * Heavy use of memcached for read amplification / hot items. MySQL sharding into many instances/partitions.
3. **Feature growth → specialized systems**

   * Photo storage (built Haystack), real-time messaging, notifications, and feed ranking demanded specialized stores and pipelines.
4. **API-first, services, infra tooling**

   * Transition to service-oriented architecture, RPC frameworks, efficient PHP execution engines (HipHop/HHVM), and internal infra (deployment, monitoring).
5. **Graph & social primitives**

   * TAO (or equivalent) — purpose-built graph access layer for social relationships with tuned caching & fanout support.
6. **Big data + ML**

   * Huge offline pipelines for analytics / personalization, plus online features (feature stores, low-latency models).
7. **Mobile / real-time / video**

   * Focus on mobile optimization, WebSocket/real-time transports, CDNs, and video delivery infrastructure.

---

# High-level architecture (text diagram)

API Clients (web/mobile)
→ Edge LB / CDN
→ API Servers / Edge tier (auth, request routing)
→ Service Layer (microservices / app servers)
→ Data Access Layer

* Cache layer (global memcached fleets, edge caches)
* Social graph store (TAO)
* Relational stores (sharded MySQL) for durable structured data
* Object store / content store (photo/video service like Haystack + CDN)
  → Asynchronous pipelines (message buses, task queues)
  → Offline systems (Hadoop-like clusters, stream processing for analytics/ML)

---

# Key components, why they exist, and typical design choices

### 1) Caching layer (memcached style)

* **Problem:** reads >> writes; database hotspots.
* **Role:** reduce DB load, serve low-latency reads for common objects (profile, tokens, counts).
* **Design points:** high memory, consistent hashing, client-side caching strategies, cache invalidation on writes.

### 2) Relational storage (sharded MySQL)

* **Problem:** complex relational queries + strong transactional semantics for some data.
* **Role:** primary authoritative store for many entities.
* **Design points:** manual or automated sharding (user-id or entity-id based), master–slave replication, failover, asynchronous replication to avoid write amplification.

### 3) Object/media store (photos, videos)

* **Problem:** massive binary content with high throughput and low-latency retrieval.
* **Role:** store raw objects and metadata; integrate with CDNs.
* **Design points:** append-optimized stores, efficient indexing, metadata in DB, global CDN for delivery.

### 4) Graph access layer (TAO / social cache)

* **Problem:** social graph queries (friends-of, likes) are read-heavy and have unique access patterns.
* **Role:** provide fast, consistent views of social relationships with caching and fanout support.
* **Design points:** denormalization for reads, asynchronous fanout, strong read caching, bounded staleness.

### 5) RPC & Execution efficiency (HipHop / HHVM / Thrift)

* **Problem:** PHP interpreted performance and inter-service RPC overhead.
* **Role:** speed up execution and provide efficient inter-service comms.
* **Design points:** compile-to-native or JIT execution; binary RPC frameworks for efficiency.

### 6) Pub/Sub and queues

* **Problem:** asynchronous tasks (notifications, feeds, email) and event-driven features.
* **Role:** decouple components, allow background processing, fanout jobs.
* **Design points:** durable queues, retry policies, idempotency, back-pressure control.

### 7) Offline data & analytics

* **Problem:** personalization & analytics at large scale.
* **Role:** batch/stream processing for recommendations, feed ranking, ads.
* **Design points:** petabyte-scale storage, distributed compute, model training & feature engineering.

### 8) Live / real-time stack

* **Problem:** real-time messaging, presence, live video.
* **Role:** maintain persistent connections, low-latency push.
* **Design points:** connection brokers, efficient push protocols, scalable presence stores.

---

# Common bottlenecks & how Facebook solved them

1. **DB hotspots** → heavy caching, sharding, and data denormalization.
2. **Fanout blowup (write fanout to many followers)** → mix of *fanout-on-write* for small audiences and *fanout-on-read* (compute feed) for huge audiences; selective precomputation.
3. **Cache invalidation complexity** → careful ownership rules, versioning, and short TTLs for volatile objects.
4. **Photo/video scaling** → dedicated object stores and tailored systems (append-only stores, metadata indexes) + global CDNs.
5. **Rapid deploys at scale** → automated deployment pipelines, canary rollouts, feature flags.
6. **Operational visibility** → highly instrumented telemetry, distributed tracing, and alerting.

---

# Typical tradeoffs & consistency models

* **Availability vs consistency:** some systems prefer eventual consistency (caches, replicas) for latency and availability. Critical ops use stronger guarantees (transactions in RDBMS).
* **Precompute vs compute-on-demand:** precomputing feeds lowers read latency but raises write cost; on-demand reduces writes but makes reads heavier. Hybrid approaches are common.
* **Denormalization:** faster reads at the cost of complex updates.

---

# Practical patterns & primitives you should remember

* **Client-side caching + consistent hashing.**
* **Sharding by stable ID (user-id, region).**
* **Read replicas & async replication.**
* **Service layer + thin API gateway.**
* **CQRS for separating read/write workloads.**
* **Bulkheads & circuit breakers for resilience.**
* **Feature flags & canary deploys.**
* **Back-pressure and graceful degradation (serve older data if fresh not available).**


---

# Concrete mini design: News Feed (short plan)

* **Write path:** user posts → append to author timeline in DB → push to followers’ inboxes for small follower set (fanout-on-write) OR enqueue job into feed-generator for large follower sets.
* **Read path:** API reads cached feed slice (memcached/edge); if cache miss, compose from precomputed inbox + pull additional items on demand and cache.
* **Ranking:** online features (recency, affinity, CTR predictions) evaluated at read time using a lightweight model; heavy ML scores precomputed offline and surfaced via features store.

---

# Key lessons / takeaways

* **Optimize for reads at massive scale** (reads usually dominate); caching & denormalization are your friends.
* **Specialize when generic systems fail** — build targeted stores for specific patterns (graph, media).
* **Hybrid approaches beat pure strategies** — e.g., mix fanout-on-write and fanout-on-read depending on audience size.
* **Operational tooling matters as much as code** — deploys, telemetry, and automation enable safe rapid change.
* **Plan for graceful degradation** — serve stale but useful content rather than fail.

---
