# API KEYS

![API Keys](/images/September-2025/29-09-2025/api_keys_overview.png)

### **1. What is an API Key?**

An **API key** is a unique identifier used to **authenticate a client or user** when they are trying to access an API (Application Programming Interface). Think of it like a **password for programs** instead of humans.

* It tells the API:
  *“Hey, I’m authorized to use you, and this is who I am.”*
* Usually, it’s a long string of letters, numbers, and sometimes symbols.

---

### **2. Why Do We Need API Keys?**

* **Authentication:** Verifies who is making the request.
* **Authorization:** Determines what the client can do (read data, write data, etc.).
* **Usage Tracking:** Monitors API usage per user for billing or limits.
* **Security:** Prevents unauthorized access and abuse of the API.

---

### **3. How API Keys Work (Step by Step)**

1. **Register for API Access:** You sign up on the API provider’s platform (e.g., OpenAI, Google Maps).

2. **Receive Your API Key:** Usually a string like `1234abcd-5678efgh-90ijklmn`.

3. **Include API Key in Requests:**

   * Example (HTTP request header):

     ```
     GET /data HTTP/1.1
     Host: api.example.com
     Authorization: Bearer YOUR_API_KEY_HERE
     ```

4. **API Validates the Key:** Checks if your key is active and has permission.

5. **Response is Returned:** If the key is valid, the API sends data back; if not, you get an error.

---

### **4. Best Practices with API Keys**

* **Keep it secret:** Never expose your key publicly (e.g., in GitHub repos).
* **Rotate regularly:** Change your keys periodically for security.
* **Use environment variables:** Don’t hardcode keys in your code.
* **Restrict usage:** Limit which IPs or services can use the key.
* **Monitor usage:** Track calls to avoid exceeding limits or misuse.

---

### **5. API Key vs OAuth vs JWT**

| Feature       | API Key              | OAuth                    | JWT                  |
| ------------- | -------------------- | ------------------------ | -------------------- |
| User Identity | Usually no user info | User-specific            | User-specific        |
| Security      | Simple, less secure  | More secure              | Secure, stateless    |
| Use Case      | Server-to-server     | Access on behalf of user | Auth & authorization |
| Expiry        | Usually permanent    | Time-limited             | Time-limited         |

---

### **6. Real-Life Example**

Imagine you’re using **OpenWeather API** to get weather data for your city:

```bash
curl "https://api.openweathermap.org/data/2.5/weather?q=London&appid=YOUR_API_KEY"
```

* `appid` = your API key.
* The server checks your key, then sends weather info if valid.

---

### ✅ Key Takeaways

* API keys are like **passwords for apps**.
* They authenticate requests, control usage, and help track API activity.
* Always **keep them secret** and **monitor their usage**.

---

## Flow

```
[Your App] 
     |
     |  Sends Request with API Key
     v
[API Server] 
     |
     |  Validates API Key
     v
[If Key Valid] ---> [Process Request & Send Data Back]
[If Key Invalid] -> [Return Error: Unauthorized]
     ^
     |
[Your App Receives Response]
```

Or even simpler version:

```
Your App --(API Key)--> API Server --(Validate)--> Data / Error --> Your App
```
