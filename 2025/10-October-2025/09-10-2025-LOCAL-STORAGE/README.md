# LOCAL STORAGE

Let’s dive into **Local Storage** — a simple yet powerful concept in **web development** and **browser-based system design**.

---

### 🧠 What is Local Storage?

**Local Storage** is a feature in web browsers that allows websites to store **data locally on the user’s device** — specifically, in the browser — in the form of **key-value pairs**.

Think of it like a **small, persistent database** in your browser.

![Local Storage](/images/2025/October-2025/09-10-2025/the-key-value-pairs-yout-49ffa01e91c7d.png)

---

### ⚙️ How it Works

When a website wants to remember something (like your dark mode preference, cart items, or login session), it can store that data in `localStorage`.

For example:

```js
// store data
localStorage.setItem("theme", "dark");

// retrieve data
let theme = localStorage.getItem("theme");

// delete a specific item
localStorage.removeItem("theme");

// clear all local storage
localStorage.clear();
```

---

### 📦 Key Features

| Feature           | Description                                                 |
| ----------------- | ----------------------------------------------------------- |
| **Storage Type**  | Key-value pairs (both stored as strings)                    |
| **Storage Limit** | Around **5–10 MB** (varies by browser)                      |
| **Persistence**   | Data **remains even after browser is closed**               |
| **Scope**         | Per domain — one website can’t access another’s storage     |
| **Synchronous**   | Accessing data is **blocking**, so not ideal for large data |

---

### 🧩 Comparison with Other Web Storage

| Storage Type       | Persistent?         | Capacity | Accessible From | Use Case                            |
| ------------------ | ------------------- | -------- | --------------- | ----------------------------------- |
| **localStorage**   | ✅ Yes               | ~5MB     | Same domain     | Save user preferences, offline data |
| **sessionStorage** | ❌ Ends on tab close | ~5MB     | Same tab        | Temporary session info              |
| **cookies**        | ✅ Yes               | ~4KB     | Sent to server  | Authentication, tracking            |


![Comparison of different storage](/images/2025/October-2025/09-10-2025/difference-local-vs-session-vs-cookies.png)

---

### 🧠 Example Use Cases

* Remembering **theme** or **language** preference
* Saving **form inputs** temporarily
* **Offline applications** (like notes, games, or to-do apps)
* **Caching API responses** to improve performance

---

### ⚠️ Limitations & Security

* Can only store **strings** (objects need `JSON.stringify` and `JSON.parse`)
* **Not encrypted**, so avoid storing sensitive data
* **Synchronous** → can slow down scripts if used heavily

---

