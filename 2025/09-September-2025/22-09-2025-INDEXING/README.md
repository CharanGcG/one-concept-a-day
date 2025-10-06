## 🔹 What is Indexing in Databases?

* An **index** is a data structure (commonly a **B-Tree** or **Hash Table**) that helps databases **find rows faster** without scanning the entire table.
* Think of it like the **index in a textbook**: instead of reading every page to find "Hashing," you jump straight to the page number listed in the index.

---

![Indexing methods](/images/September-2025/22-09-2025/database%20indexing.png)

## 🔹 Why Indexing Matters in System Design (Scalability)

When systems scale (millions of users, billions of records), queries must stay fast. Indexing:

1. **Reduces Query Latency** – Speeds up `SELECT` queries by avoiding full table scans.
2. **Supports High Read Scalability** – Essential in read-heavy systems like social media feeds, search, and e-commerce.
3. **Enables Efficient Filtering & Sorting** – Indexes optimize queries with `WHERE`, `ORDER BY`, and `JOIN`.

---

## 🔹 Types of Indexes

1. **Primary Index**

   * Built on the primary key.
   * Ensures uniqueness & fast lookup.

2. **Secondary Index**

   * Built on non-primary columns (e.g., `email`, `created_at`).
   * Useful for searching/filtering beyond the primary key.

3. **Composite Index**

   * Index on multiple columns (`(user_id, created_at)`).
   * Helps queries like *“Get all posts by a user, ordered by creation date.”*

4. **Unique Index**

   * Prevents duplicate values in a column.

5. **Full-text Index**

   * Used for searching words inside text columns (e.g., Google-like search).

6. **Hash Index** (common in NoSQL, sometimes in RDBMS)

   * Fast lookups for equality checks (`WHERE user_id = 123`).
   * Not great for range queries (`WHERE user_id BETWEEN 100 AND 200`).

---

## 🔹 Trade-offs of Indexing

Indexes make **reads faster**, but they:

* **Slow down writes**: Every `INSERT`, `UPDATE`, or `DELETE` must also update the index.
* **Take storage space**: Large tables with many indexes consume significant disk and memory.
* **Require careful design**: Wrong indexes can make queries slower.

---

## 🔹 Indexing & Scalability in System Design

1. **OLTP Systems (e.g., Banking, E-commerce transactions)**

   * Balance between **fast writes** and **indexed reads**.
   * Usually rely on **primary + a few secondary indexes**.

2. **OLAP Systems (Analytics, BI dashboards)**

   * Often use **columnar databases + bitmap indexes**.
   * Optimized for large aggregations.

3. **Distributed Databases (Sharded systems, NoSQL)**

   * Indexes may be **local to a shard** → finding data across shards might need a **scatter-gather query**.
   * Some systems (like **Elasticsearch**) are specialized search engines optimized for indexing.

---

## 🔹 Analogy

Imagine a **library with millions of books**:

* Without index → You’d check every book to find “Harry Potter.”
* With index → The catalog points you to **Row 12, Shelf 4, Book 27** instantly.
* But updating the catalog every time a book is moved or added takes extra work (same as index maintenance in DB).

---

## 🔹 When to Use Indexes (Design Rules of Thumb)

✅ Create indexes on:

* Columns used in `WHERE` clauses frequently.
* Columns used in `JOIN` operations.
* Columns used in sorting/ordering.

❌ Avoid indexing:

* Columns with very few distinct values (e.g., gender = M/F).
* Tables with heavy writes unless queries absolutely need it.

---

⚡ **In summary:**
Indexing is a **core system design tool** that trades **storage + write cost** for **fast read scalability**. The right indexing strategy ensures queries scale efficiently as data grows.

---


Let’s break it down with your **"find Charan G's birthday in a billion-user social media table"** example.

![Charan G B+ tree](/images/September-2025/22-09-2025/Gemini_Generated_Image_4kur544kur544kur.png)

## 🟢 Case 1: Without Index

Imagine your database has a **Users table** like this:

| user\_id    | name     | birthday   | email                                       |
| ----------- | -------- | ---------- | ------------------------------------------- |
| 1           | Alice K  | 1999-01-01 | [alice@email.com](mailto:alice@email.com)   |
| ...         | ...      | ...        | ...                                         |
| 999,999,999 | Charan G | 2004-10-26 | [charangcg4@gmail.com](mailto:charangcg4@gmail.com) |

Query:

```sql
SELECT birthday FROM users WHERE name = 'Charan G';
```

* The database **doesn’t know where Charan G is** in the 1 billion rows.
* It performs a **full table scan** → checks row 1, then row 2, then row 3… until it finds you.
* **Time complexity**: `O(N)` → very slow at scale (billions of comparisons).

---

## 🟢 Case 2: With Index on `name`

If we create an index:

```sql
CREATE INDEX idx_name ON users(name);
```

The database builds an **index data structure** (usually a **B+ Tree**):

```
Index on `name`
---------------------------------
'Alice K'   -> row pointer (1)
'Bob M'     -> row pointer (2)
...
'Charan G'  -> row pointer (999,999,999)
...
'Zoe Z'     -> row pointer (some row)
```

Now, when you run:

```sql
SELECT birthday FROM users WHERE name = 'Charan G';
```

* Instead of scanning every row, the DB jumps into the **B+ Tree index**.
* B+ Tree lookup = **O(log N)** (logarithmic time).
* For 1 billion users, that’s around **30 steps max** (because log₂(1,000,000,000) ≈ 30).
* Once it finds the pointer to your row → it directly fetches the birthday.

---

## 🟢 Analogy

* **Without index**: Searching for "Charan G" in a phonebook that’s unsorted → you must read every single name until you find yours.
* **With index**: The phonebook is alphabetically sorted with a quick lookup system → you can jump straight to the "C" section, then narrow down to "Charan G".

---

## ⚡ Why It’s Powerful in System Design

* At **small scale**, both full scan and index lookup feel fast.
* At **web scale (billions of records)**, indexes reduce query times from **minutes → milliseconds**.
* That’s why social media, e-commerce, and search engines rely heavily on indexing.

---

👉 So in your case:
Indexing means the DB **doesn’t check all billion rows**—it checks only \~30 index levels to locate your exact row, then fetches the birthday directly.

---


Perfect 🔥 You’re asking for the **step-by-step flow** of what actually happens inside the database engine when you query using an index. Let’s go through it in **technical terms** with your “find Charan G’s birthday” example.

---

## 🟢 Flow of Query Execution with Index

### 1. **SQL Query Submitted**

```sql
SELECT birthday FROM users WHERE name = 'Charan G';
```

* The query is passed to the **SQL parser** → it validates syntax and converts it into an **execution plan**.

---

### 2. **Query Optimizer Decides Execution Path**

* The **optimizer** checks:

  * Does a usable index exist on `name`? (yes → `idx_name`)
  * Should it use the index, or would a full table scan be cheaper?
* Since we’re filtering on a **specific value**, the optimizer chooses the **index scan** strategy.

---

### 3. **Index Lookup (B+ Tree Traversal)**

* The database goes to the **B+ Tree index on `name`**.
* Steps:

  1. Start at the **root node** of the tree.
  2. Compare "Charan G" with key ranges in the node.
  3. Follow the pointer down to the appropriate **child node**.
  4. Repeat until reaching a **leaf node**.
  5. Find the exact key `"Charan G"` in the leaf.
* **Time complexity:** `O(log N)` comparisons (≈ 30 for 1B rows).

At this point, the DB doesn’t yet have the birthday.
👉 What it found is the **row pointer (row\_id)** for `"Charan G"` in the table.

---

### 4. **Row Lookup in the Table (Heap or Clustered Storage)**

* Using the row pointer, the DB performs a **direct fetch** from the data file.
* It retrieves the full row:

  ```
  user_id: 999,999,999
  name: "Charan G"
  birthday: "2004-10-26"
  email: "charangcg4@gmail.com"
  ```
* This is called a **table lookup** (or **bookmark lookup** in some engines).

---

### 5. **Projection (Return Only Requested Column)**

* Since the query only asked for `birthday`, the DB engine **filters out unnecessary columns**.
* Returns:

  ```
  2004-10-26
  ```

---

### 6. **Result Sent Back**

* The execution engine sends the result to the client application.

---

## 🔹 Technical Flow (Summarized)

1. **Query Parser** → validate SQL
2. **Optimizer** → choose index scan plan
3. **B+ Tree Index Lookup** → find row pointer(s)
4. **Table Access** → fetch actual row(s)
5. **Projection** → return requested fields
6. **Result** → client receives birthday

---

⚡ **Key Point:**
The **index stores only keys + pointers**, not the actual birthday. That’s why after finding `"Charan G"` in the index, the database **must still fetch the row** from the main table.

(Unless it’s a **covering index** → which stores extra columns in the index itself, allowing the DB to answer the query *without touching the table*.)

---

A **B-Tree (and its database variant, the B+ Tree)** is a **multi-way search tree** → unlike a binary tree (2 children max), **each node can hold multiple keys and multiple children**.


![b tree structure](/images/September-2025/22-09-2025/b%20tree.jpg)

## 🟢 Structure of a B-Tree Node

* Each **node** contains:

  * **Keys**: multiple sorted values (e.g., names, IDs).
  * **Pointers/Children**: links to subtrees or (in leaf nodes) row pointers.

For example, suppose we have an index on `name`:

```
Root Node:
[Adam | John | Sarah]
 |       |       |
 v       v       v
```

* If you’re searching `"Charan"`, you compare it against `"Adam"`, `"John"`, `"Sarah"`.

  * `"Charan"` > `"Adam"` but < `"John"` → follow the **second pointer**.

---

## 🟢 Why Multiple Keys?

1. **Disk Efficiency**

   * Databases store nodes as **pages (blocks)** on disk (like 4KB each).
   * Instead of 1 key per node (like binary trees), a node can pack **hundreds of keys** into one page.
   * This minimizes disk reads → fewer I/O operations = faster lookups.

2. **Balanced Height**

   * Because each node holds many keys, the tree height stays **shallow** even for billions of records.
   * For example:

     * Binary tree for 1B entries → height \~30.
     * B+ Tree with 100 keys per node → height only \~4–5.

---

## 🟢 B+ Tree vs B-Tree (databases use B+ Trees usually)

* **B-Tree**: keys + pointers stored in both internal nodes and leaf nodes.
* **B+ Tree**:

  * Internal nodes only store keys for navigation.
  * **All actual row pointers are stored in the leaf nodes**.
  * Leaves are linked together in a **linked list** → makes range queries (`BETWEEN`, `ORDER BY`) super efficient.

Example (Index on `name`):

```
Internal Node:
[Adam | John | Sarah]

Leaf Nodes:
[Adam -> row#101] → [Charan -> row#202] → [John -> row#303] → [Sarah -> row#404]
```

So when you search `"Charan"`:

1. Navigate internal nodes down to correct leaf.
2. At leaf: find `"Charan"` → pointer → actual row in table.

---

⚡ That’s why databases love B+ Trees → shallow depth + efficient range scans + fewer disk reads.

---
