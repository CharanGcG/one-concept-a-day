# CACHING

## 🧠 What is Caching?

**Caching** is the process of **storing frequently accessed data in a fast-access storage layer** (like memory) so that future requests for that data can be served **faster**.

In simple terms:

> “Instead of fetching data from the slow source every time, keep a copy somewhere faster.”

![Caching](/images/2025/October-2025/08-10-2025/Cache-Working.jpg)

---

## ⚙️ Real-world analogy

Imagine you’re a student referring to a big textbook.

* Without cache: you open the heavy book every time you need a formula.
* With cache: you note the important formulas on a sticky note — next time, you look there first.

The sticky note = cache 🟨
The textbook = database 🧾

---

## 🧩 System Design Perspective

Here’s what happens step-by-step:

```
User -> Application Server -> Cache -> Database
```

1. User makes a request (e.g., “get user profile”).
2. Server checks **cache** (e.g., Redis, Memcached) first.

   * ✅ If found → return it directly (cache hit).
   * ❌ If not found → fetch from DB (cache miss), then store result in cache for next time.

---

## 🧭 Types of Caching

| Type                   | Description                            | Example                                  |
| ---------------------- | -------------------------------------- | ---------------------------------------- |
| **Client-side cache**  | Stored in user’s browser/app           | Browser caching (images, CSS)            |
| **CDN cache**          | Cached at edge servers near users      | Cloudflare, Akamai                       |
| **Application cache**  | In-memory caching at app level         | Using `Guava Cache`, `Ehcache`           |
| **Database cache**     | Between DB and app                     | Redis, Memcached                         |
| **Query result cache** | Stores results of expensive DB queries | MySQL query cache, PostgreSQL plan cache |


![Client side caching](/images/2025/October-2025/08-10-2025/client-side-caching.png)

---

## 🧮 Common Cache Strategies

| Strategy                       | Description                                            | Example                               |
| ------------------------------ | ------------------------------------------------------ | ------------------------------------- |
| **Write-through**              | Data written to cache and DB simultaneously            | Ensures consistency                   |
| **Write-back / Write-behind**  | Data written to cache, DB updated later asynchronously | Better performance, risk of data loss |
| **Write-around**               | Writes go directly to DB, cache updated only on reads  | Prevents cache pollution              |
| **Cache-aside (lazy loading)** | App checks cache first, loads from DB if missing       | Most common pattern                   |


![Caching Strategies](/images/2025/October-2025/08-10-2025/caching%20strategies.webp)

---

## 🧨 Cache Invalidation (The hardest problem!)

> “There are only two hard things in Computer Science: cache invalidation and naming things.” — Phil Karlton

When data changes in the DB, how do we keep the cache consistent?

* **Time-based expiration (TTL):** Cache auto-expires after a duration.
* **Manual invalidation:** Remove cache entry when DB changes.
* **Versioning:** Use version numbers or timestamps to detect stale data.

---

## 🧱 Popular Tools

| Layer            | Common Tools                                   |
| ---------------- | ---------------------------------------------- |
| In-memory cache  | **Redis**, **Memcached**, **Hazelcast**        |
| Java-based cache | **Ehcache**, **Caffeine**, **Guava Cache**     |
| CDN cache        | **Cloudflare**, **Akamai**, **AWS CloudFront** |

---

## 🚀 Example in Java (using Redis)

```java
Jedis jedis = new Jedis("localhost");

// Try to get data from cache
String userData = jedis.get("user:101");

if (userData == null) {
    // Cache miss - fetch from DB
    userData = getUserFromDatabase(101);
    
    // Store in cache with TTL (e.g., 60 seconds)
    jedis.setex("user:101", 60, userData);
}

System.out.println("User data: " + userData);
```

---

## 🧠 Summary

| Concept           | Meaning                    |
| ----------------- | -------------------------- |
| **Goal**          | Reduce latency and DB load |
| **Storage**       | Usually in-memory (fast)   |
| **Trade-off**     | Consistency vs performance |
| **Key Challenge** | Cache invalidation         |

---


Let’s go deep into **cache invalidation** — it’s one of the trickiest and most important topics in caching and system design.


## 🧠 What is Cache Invalidation?

**Cache invalidation** is the process of **removing or updating stale (outdated) data** from a cache when the original data in the database changes.

In simpler terms:

> If your cache has old data that’s no longer correct, you need to “invalidate” (delete or update) it so users always get fresh data.

---

## 🧩 Why It’s Needed

Caching gives **speed**, but can cause **inconsistency**.

Example:

* You store `user:101 → "Name = Charan"` in cache.
* Later, Charan changes his name to “Chaaru” in the database.
* But your cache still says `"Charan"`.
  → That’s **stale data** ❌

To fix this, you need **cache invalidation** — remove or refresh that old value.

---

## ⚙️ How Cache Invalidation Works

There are **3 main approaches** used in real systems:

| Method                       | Description                                                                   | Pros               | Cons                              |
| ---------------------------- | ----------------------------------------------------------------------------- | ------------------ | --------------------------------- |
| **1. Time-to-Live (TTL)**    | Each cache entry expires after a fixed time (e.g., 5 mins).                   | Simple             | May serve stale data until expiry |
| **2. Explicit Invalidation** | When DB updates, app manually deletes/updates cache.                          | Accurate           | Requires extra logic in code      |
| **3. Versioning**            | Use version numbers or timestamps in cache keys. New updates create new keys. | Avoids stale reads | Increases memory use              |

---

## 🧱 Real Example

Let’s say your app caches product info:

```
Cache key: product:123
Value: { name: "iPhone 14", price: 70000 }
```

Now someone updates the price in the database to ₹75,000.
You have two choices:

1. **Invalidate the cache**
   → Delete key `product:123`
   → Next read will fetch new data from DB and re-cache it.
2. **Update the cache**
   → Directly set the new price in cache.

---

## 💡 Invalidation Patterns (System Design Level)

| Pattern                        | What it does                                     | Example                                         |
| ------------------------------ | ------------------------------------------------ | ----------------------------------------------- |
| **Write-through**              | Update DB and cache together                     | `cache.set(key, newValue)` right after DB write |
| **Write-around**               | Write only to DB, cache will update on next read | Slower for first read                           |
| **Write-back**                 | Write to cache first, sync to DB later           | High performance, risk of data loss if crash    |
| **Cache-aside (Lazy Loading)** | App checks cache, updates it only on miss        | Most common in distributed systems              |

---

## ⚔️ Challenges (Why It’s “Hard”)

1. **Race conditions:** DB updates before cache invalidation, or vice versa.
2. **Distributed caches:** Hard to keep multiple cache nodes consistent.
3. **Network delays:** Cache delete happens slightly late → stale read.
4. **Complex dependency:** Updating one entity may affect many cache keys.

---

## 🧠 Quick Diagram

```
        ┌──────────────┐
Request │    Client     │
        └──────┬───────┘
               │
        ┌──────▼───────┐
        │     Cache     │
        │ (Redis, etc.) │
        └──────┬───────┘
   Cache miss  │
               ▼
        ┌──────────────┐
        │   Database    │
        └──────────────┘
```

🌀 When DB data changes → Cache must be **invalidated** or **updated**.

---

## ✅ Summary

| Key Point     | Explanation                             |
| ------------- | --------------------------------------- |
| **What**      | Removing/refreshing outdated cache data |
| **Why**       | To keep data consistent with the DB     |
| **How**       | TTL, manual invalidation, or versioning |
| **Hard Part** | Timing and coordination across systems  |

---

