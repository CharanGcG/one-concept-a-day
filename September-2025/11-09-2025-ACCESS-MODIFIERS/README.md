# 📘 Access Modifiers in Java

## 🔹 Introduction

Access modifiers in Java are keywords that **control the visibility (scope)** of classes, variables, methods, and constructors.
They are fundamental to **object-oriented programming (OOP)** because they enable encapsulation, security, and maintainability.

---

## 🔹 Types of Access Modifiers

| Modifier      | Same Class | Same Package | Subclass (diff package) | Other Packages |
| ------------- | ---------- | ------------ | ----------------------- | -------------- |
| **public**    | ✅          | ✅            | ✅                       | ✅              |
| **protected** | ✅          | ✅            | ✅                       | ❌              |
| **default**   | ✅          | ✅            | ❌                       | ❌              |
| **private**   | ✅          | ❌            | ❌                       | ❌              |


![Access modifiers](/images/September-2025/11-09-2025/access-modifiers-in-java.png)

### 1. **public**

* Visible everywhere in the program.
* No restrictions on access.

```java
public class Example {
    public void show() {
        System.out.println("Public method");
    }
}
```

### 2. **protected**

* Visible:

  * Within the same package.
  * In subclasses (even if in a different package).

```java
class Parent {
    protected void display() {
        System.out.println("Protected method");
    }
}
```

### 3. **default** (no keyword)

* Package-private.
* Accessible only within the same package.

```java
class Example {
    void print() {   // default access
        System.out.println("Default method");
    }
}
```

### 4. **private**

* Accessible only within the same class.
* Used for **encapsulation**.

```java
class Example {
    private int secret = 42;

    private void reveal() {
        System.out.println(secret);
    }
}
```

---

## 🔹 Why Java Uses Access Modifiers

1. **Encapsulation & Data Hiding**

   * Protects data by restricting direct access.
   * Example: `BankAccount` hides `balance` and allows safe operations through `deposit()` and `getBalance()`.

2. **Security**

   * Prevents unauthorized access to sensitive data or internal APIs.
   * Keeps implementation details hidden.

3. **Maintainability**

   * Internal logic can be changed without affecting external code.
   * Example: `ArrayList` hides its internal array but provides safe public methods.

4. **Inheritance Control**

   * `protected` allows subclasses to reuse/extend functionality without exposing it to everyone.

---

## 🔹 Java vs Other Languages

### 🔸 C

* No built-in access modifiers.
* Relies on `static` and header files for scope control.
* Easy to break encapsulation.

### 🔸 C++

* Has `public`, `protected`, `private`.
* Supports `friend` classes/functions (can bypass encapsulation).
* Java does **not** allow `friend`, so encapsulation is stricter.

### 🔸 Python

* Uses conventions (`_var`, `__var`) instead of true enforcement.
* All members are technically accessible.
* Java enforces restrictions at compile-time → safer.

### 🔸 Java

* **Strict, consistent, and enforced**.
* Promotes strong encapsulation and secure APIs.

---

## 🔹 Key Takeaways

* **public** → accessible everywhere.
* **protected** → package + subclasses.
* **default** → package only.
* **private** → class only.

Java enforces these rules at **compile time**, making it more robust and secure than convention-based or loose access systems in other languages.

---

## 🔹 Quick Example: Bank Account

```java
class BankAccount {
    private double balance;   // hidden data

    public void deposit(double amount) {
        if (amount > 0) balance += amount;
    }

    public double getBalance() {
        return balance;
    }
}
```

✅ Users can deposit and check balance.
❌ Users cannot directly manipulate `balance`.

---

## ✅ Conclusion

Java’s access modifiers are essential for:

* **Encapsulation**
* **Security**
* **Maintainability**
* **Inheritance Control**

They make Java’s OOP design stricter and safer compared to C, C++, and Python.

---
