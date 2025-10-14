# SESSION STORAGE

## **1. What is Session Storage?**

**Session Storage** is a type of **web storage** provided by browsers that allows you to store data **temporarily** for a single browser tab or window session.

* The data is stored **per tab** — if you open the same site in another tab, the data won’t be shared.
* Data is **cleared automatically** when the tab or browser is closed.
* It’s similar to **Local Storage**, but with a shorter lifespan (session-limited).

![Session storage](/images/2025/October-2025/10-10-2025/session-storage.png)

---

## **2. Key Characteristics**

| Feature                  | Session Storage         | Local Storage                    | Cookie               |
| ------------------------ | ----------------------- | -------------------------------- | -------------------- |
| Lifespan                 | Until tab/window closed | Persist until explicitly removed | Can set expiration   |
| Storage Limit            | ~5-10 MB                | ~5-10 MB                         | ~4 KB                |
| Sent with every request? | No                      | No                               | Yes (sent to server) |
| Scope                    | Single tab              | Across tabs/windows              | Across tabs/windows  |

---

## **3. Why use Session Storage?**

* To **temporarily store data** that’s needed only during the user’s session.
* Faster than cookies since it’s **client-side only** and **not sent to the server**.
* Common use cases:

  * Storing form input temporarily.
  * Keeping UI state (like active tabs) within a session.
  * Caching temporary data for dynamic pages.

---

## **4. How to use it (JavaScript)**

### **Set data**

```javascript
// key = 'username', value = 'Charan'
sessionStorage.setItem('username', 'Charan');
```

### **Get data**

```javascript
let user = sessionStorage.getItem('username');
console.log(user); // Output: Charan
```

### **Remove data**

```javascript
sessionStorage.removeItem('username');
```

### **Clear all session storage**

```javascript
sessionStorage.clear();
```

---

## **5. Differences from Local Storage (quick recap)**

* **Session Storage:** temporary, per tab, cleared on close.
* **Local Storage:** permanent (until manually cleared), shared across tabs.
* **Use case difference:** Session storage → “session-specific data”, Local storage → “persistent data”.

![Differences](/images/2025/October-2025/09-10-2025/difference-local-vs-session-vs-cookies.png)

---

## **6. Browser Support**

All modern browsers support Session Storage (Chrome, Firefox, Edge, Safari, etc.).
Check availability before using:

```javascript
if (typeof(Storage) !== "undefined") {
    // Session storage is supported
}
```

---

💡 **Tip:** If you want the same data across tabs, use **Local Storage**. If you want data **isolated per tab session**, use **Session Storage**.

---


## **1. Common Data Stored in Session Storage**

Session Storage is meant for **temporary, session-specific data**. Typical examples include:

1. **User Interface (UI) State**

   * Which tab or section a user is viewing.
   * Scroll positions.
   * Last opened modal or form.

2. **Form Data (Temporary)**

   * Drafts of forms that a user is filling out so that if they refresh, the data isn’t lost.
   * Multi-step form progress (step number, partial input).

3. **Session Tokens / Temporary Auth Data**

   * Short-lived tokens used only during a single session (never permanent login tokens).
   * e.g., a CSRF token valid for the session.

4. **Temporary Filters / Search Preferences**

   * A user’s temporary search filters on a page.
   * Sort orders that are not meant to be saved permanently.

5. **Small Temporary Data Objects**

   * Items in a cart (for single-session shopping).
   * Page-specific counters or flags.

---

## **2. Best Practices for Session Storage**

1. **Keep it small**

   * Usually under **5 MB**.
   * Don’t try to store large files (like images, videos).

2. **Don’t store sensitive data**

   * Session Storage is client-side and accessible via JavaScript.
   * Never store passwords, permanent tokens, or sensitive personal info.

3. **Use JSON for objects**

   ```javascript
   let user = { name: 'Charan', age: 21 };
   sessionStorage.setItem('user', JSON.stringify(user));

   let retrievedUser = JSON.parse(sessionStorage.getItem('user'));
   ```

4. **Use consistent keys**

   * Prefer a naming convention like `pageName_variableName` to avoid conflicts.
   * Example: `checkout_cartItems` instead of just `cart`.

5. **Clear data when no longer needed**

   * If the data is session-specific, clear it once the session ends or user logs out:

   ```javascript
   sessionStorage.removeItem('cartItems');
   sessionStorage.clear(); // clears all session storage
   ```

6. **Feature detection**

   * Always check if the browser supports Session Storage:

   ```javascript
   if (window.sessionStorage) {
       // safe to use
   }
   ```

7. **Avoid overusing it**

   * Session Storage should **enhance UX**, not act as a database.
   * For permanent storage, prefer Local Storage or server-side storage.

---

💡 **Quick Rule of Thumb:**

> *If it’s temporary, per-tab, and not sensitive → sessionStorage is okay.*
> *If it’s permanent, sensitive, or needs cross-tab persistence → use Local Storage or server-side storage.*

---

