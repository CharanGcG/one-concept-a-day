# CORS (Cross-Origin Resource Sharing)

![Cors](/images/September-2025/30-09-2025/fetching-page-cors.svg)

### **1. What is CORS?**

CORS is a **security feature implemented by browsers** to control how web pages from one origin (domain) can request resources from another origin.

* **Origin** = combination of **protocol + domain + port**.

  * Example:

    * `https://example.com` and `http://example.com` → **different origins** (different protocol)
    * `https://api.example.com` and `https://example.com` → **different origins** (different subdomain)

* **Problem CORS solves**:
  Without CORS, a malicious site could make requests to another site on your behalf (like your bank) and steal sensitive data. This is called a **cross-origin request**.

---

### **2. How CORS Works**

1. Browser sends a request to a different origin.
2. Server responds with **CORS headers** that tell the browser whether the request is allowed.
3. If allowed → browser gives your web page access to the response.
   If not allowed → browser blocks access.

**Key headers:**

* `Access-Control-Allow-Origin` → tells which origins can access the resource.

  * Example: `Access-Control-Allow-Origin: https://example.com`
* `Access-Control-Allow-Methods` → allowed HTTP methods (GET, POST, PUT…)
* `Access-Control-Allow-Headers` → allowed headers in the request
* `Access-Control-Allow-Credentials` → allows cookies or credentials

---

### **3. Types of Requests**

1. **Simple Requests**:

   * GET, POST (with simple content-types)
   * Browser directly sends request → checks headers → decides if allowed

2. **Preflighted Requests**:

   * PUT, DELETE, or requests with custom headers
   * Browser first sends an **OPTIONS** request (preflight) to check if it’s safe
   * If server allows → actual request is sent

---

### **4. Example Flow**

Suppose your frontend at `https://myapp.com` wants data from `https://api.example.com/data`.

**Browser sends:**

```
GET /data HTTP/1.1
Host: api.example.com
Origin: https://myapp.com
```

**Server responds:**

```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://myapp.com
Content-Type: application/json
```

* ✅ Browser allows JavaScript to read response
* ❌ If server didn’t include header → browser blocks it

---

### **5. Quick Diagram (Text Version)**

```
Frontend (myapp.com) ---- request ---> Backend (api.example.com)
         |                                 |
         | <---- CORS headers -------------|
         |
   Browser checks: allowed? yes → data accessible
```

---

### **6. Common Errors**

* `Access to fetch at '...' from origin '...' has been blocked by CORS policy`

  * Means the server **did not allow your origin** in its CORS settings
* Fix:

  * Server must add your origin to `Access-Control-Allow-Origin`
  * For dev: can use `*` to allow all origins (not recommended in production)

---

CORS is basically the **browser enforcing “who can talk to who”** on the web. Servers control it, browsers enforce it.

---

### **CORS = Club Bouncer Analogy**

1. **The Club** = The **server** (like `api.example.com`)
2. **The Guest** = Your **web page/frontend** (like `myapp.com`)
3. **The Bouncer** = The **browser**

---

#### **Scenario 1: Allowed Guest**

* Guest: “Hey, can I come in and grab some drinks?”
* Bouncer checks the guest’s ID (Origin) → sees it’s **on the list of allowed guests**.
* ✅ Guest is allowed in → gets drinks (data)

> **CORS header example:** `Access-Control-Allow-Origin: https://myapp.com`

---

#### **Scenario 2: Uninvited Guest**

* Guest: “Hey, I’m from a different town, can I come in?”
* Bouncer checks the list → not allowed
* ❌ Guest is blocked → no drinks (browser blocks access)

---

#### **Scenario 3: VIP Guests (Preflight)**

* Some guests want **special treatment** (like bringing big bags or unusual requests → PUT, DELETE, custom headers)
* Bouncer says: “Hold on, I need to check with the manager first” (OPTIONS preflight request)
* Manager (server) approves → guest gets access
* Manager rejects → guest is denied

---

#### **Key Takeaways**

* **Bouncer = browser** → enforces the rules
* **Club manager = server** → sets the rules
* **Guest = frontend app** → wants to access the club (server data)


---

### **Why specifying frontends in CORS is important**

1. **Frontend = the “origin”**

   * Each web page running in a browser has an origin (`protocol + domain + port`).
   * Browsers enforce CORS **automatically**.

2. **Without proper CORS headers**

   * Any website could try to make requests to your API from a user’s browser.
   * Browser will block the response **only if CORS headers are missing or wrong**.
   * But if you set CORS too loosely (like `*`), **any web page can access your API**.

3. **With proper CORS headers**

   * You specify exactly which frontends are allowed.
   * Example:

     ```http
     Access-Control-Allow-Origin: https://mywebsite.com
     ```
   * Only your web pages can successfully call the API from a browser.

---

### **Important Notes**

* **CORS ≠ full security**

  * It **doesn’t protect server-to-server calls**.
  * Always combine with **API keys, tokens, or other auth**.

* **Best practice**

  * Specify only your trusted frontends.
  * Avoid using `*` in production unless the API is truly public.

---


**CORS is specifically about frontend origins.**

* It’s a **browser-enforced security mechanism**.
* It tells the browser: *“Only allow JavaScript running on these specific web pages (origins) to access this server’s resources.”*
* If the request comes from an **unlisted origin**, the browser blocks it — your server never has to reject it itself (though it should still check authorization).

---

**Key point to remember:**

| Aspect              | CORS                                                   |
| ------------------- | ------------------------------------------------------ |
| Who enforces?       | Browser                                                |
| Purpose             | Control which **frontend origins** can access your API |
| Does it check auth? | No, just origin                                        |
| Server-only calls   | Not needed                                             |

---

So in short: **CORS ≈ “frontend access whitelist”** enforced by the browser.

