Perfect 🔥 let’s move on to the **Runtime Data Areas (JVM Memory Model)**. I’ll keep the same **README-style documentation** format for consistency.

---

# 📘 JVM Runtime Data Areas – Documentation

## 🔹 What are Runtime Data Areas?

The **Runtime Data Areas** are the **memory regions inside the JVM** where program data is stored during execution.

* Each Java program gets these memory areas when the JVM starts.
* Some areas are **shared across all threads**, while others are **private to each thread**.

👉 Think of it as the **RAM layout of the JVM**.

---

## 🧩 Components of Runtime Data Areas

### 1. **Method Area (a.k.a. Class Area / MetaSpace)**

* **Shared** across all threads.
* Stores class-level metadata:

  * Class names, method names, field names.
  * Bytecode for methods.
  * Static variables.
  * Runtime constant pool (symbols, references).
* In Java 8+, **stored in Metaspace** (uses native memory instead of heap).

---

### 2. **Heap Area**

* **Shared** memory region.
* Stores all **objects** and **instance variables**.
* Garbage Collector (GC) operates here.
* Divided into:

  * **Young Generation** (new objects, frequent GC).
  * **Old Generation (Tenured)** (long-lived objects).
  * **Permanent Generation (PermGen)** → replaced by Metaspace in Java 8.

---

### 3. **JVM Stack (per-thread)**

* Each thread gets its own **stack**.
* Stores **stack frames** for method calls.
* A stack frame contains:

  * Local variables.
  * Operand stack (intermediate calculations).
  * Reference to runtime constant pool.
* When a method is called → a new frame is pushed.
* When method exits → frame is popped.

---

### 4. **Program Counter (PC) Register (per-thread)**

* Each thread has its own PC register.
* Stores the **address of the current JVM instruction** being executed.
* Helps JVM resume execution after method calls or branches.

---

### 5. **Native Method Stack (per-thread)**

* Supports execution of **native (non-Java) code**, usually written in C/C++.
* Stores state of native method invocations.
* Works with JNI (Java Native Interface).

---

## 📊 Runtime Data Areas Structure

```
Runtime Data Areas
 ├── Shared by All Threads
 │     ├── Method Area (class metadata, static vars, bytecode)
 │     └── Heap (objects, instance vars, GC managed)
 │
 └── Per Thread
       ├── JVM Stack (stack frames: local vars, operands, references)
       ├── Program Counter Register (current instruction)
       └── Native Method Stack (for JNI/native calls)
```

---

## ⚡ Example Walkthrough – Memory in Action

```java
class Student {
    static String school = "ABC High"; // Method Area
    String name; // Heap

    Student(String name) {
        this.name = name; // Heap
    }

    void greet() {
        String msg = "Hello, " + name; // JVM Stack (local var)
        System.out.println(msg);
    }

    public static void main(String[] args) {
        Student s1 = new Student("Alice"); // Heap
        s1.greet();
    }
}
```

**Execution Flow:**

1. `Student` class is loaded → metadata + `school` stored in **Method Area**.
2. `s1` object is created → stored in **Heap**.
3. `greet()` is called → new **Stack Frame** created in JVM Stack.
4. Local variable `msg` stored in **JVM Stack**.
5. PC Register tracks which bytecode instruction is being executed.
6. If `System.out.println` needs native code → handled in **Native Method Stack**.

---

## 📌 Summary

* **Shared Areas:** Method Area + Heap.
* **Per Thread Areas:** JVM Stack, PC Register, Native Method Stack.
* Heap → for objects.
* Method Area → for class metadata.
* Stacks → for method execution.
* PC Register → instruction pointer.
* Native Method Stack → JNI/native code support.

👉 Together, these areas form the **memory backbone** of the JVM.

---

Would you like me to go even deeper and write a **README for the Class Loader Subsystem** next? (since it’s another core part of JVM alongside Runtime Data Areas & Execution Engine)
