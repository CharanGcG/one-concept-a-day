# 📘 Java Virtual Machine (JVM) – Documentation

## 🔹 What is JVM?

The **Java Virtual Machine (JVM)** is a **software-based engine** that executes Java bytecode.

* It provides **platform independence**: write Java code once, run it anywhere.
* It acts as an **abstraction layer** between Java programs and the underlying operating system + hardware.
* The JVM is part of the **JRE**, which in turn is part of the **JDK**.

👉 Think of JVM as a **virtual computer inside your real computer**, with its own class loading, memory management, and execution process.

---

## 🧩 Components of JVM

### 1. **Class Loader Subsystem**

Responsible for **loading classes into memory dynamically** (only when needed, not all at once).

**Steps inside Class Loader:**

1. **Loading**

   * Loads `.class` (bytecode) files from disk, network, or JAR files.
   * Uses three loaders (delegation model):

     * **Bootstrap ClassLoader** → loads core Java classes (`java.lang.*`).
     * **Extension ClassLoader** → loads extensions (`javax.*`, optional libs).
     * **Application ClassLoader** → loads user-defined classes.

2. **Linking**

   * **Verification** → Ensures bytecode is safe (e.g., no stack overflows, illegal casts).
   * **Preparation** → Allocates memory for static variables, sets default values.
   * **Resolution** → Replaces symbolic references (e.g., class names) with actual memory addresses.

3. **Initialization**

   * Assigns real values to static variables.
   * Executes static blocks.

**🔑 Benefit:**

* On-demand loading = better memory usage.
* Enforces Java’s **security sandbox** by verifying bytecode before running.

---

### 2. **Runtime Data Areas (Memory Model)**

The JVM divides memory into different areas to manage program execution:

* **Method Area (a.k.a. MetaSpace in Java 8+)**

  * Stores class structure (methods, fields, bytecode).
  * Shared across threads.

* **Heap Area**

  * Stores objects and instance variables.
  * Shared memory, managed by Garbage Collector.

* **JVM Stacks (per-thread)**

  * Each thread has its own stack.
  * Contains **stack frames**, which hold:

    * Local variables
    * Operand stack (for intermediate results)
    * Method return values

* **Program Counter (PC) Register**

  * Each thread has its own PC register.
  * Stores the **address of the current executing instruction**.

* **Native Method Stack**

  * Stores information for native (non-Java) method calls, usually C/C++ code via JNI.

---

### 3. **Execution Engine**

The **brain** of the JVM — executes bytecode.

* **Interpreter**

  * Reads and executes bytecode instruction by instruction.
  * Simple, but slow for repeated code.

* **JIT Compiler (Just-In-Time)**

  * Detects frequently used methods (“hotspots”).
  * Compiles them into native machine code for faster performance.
  * Optimizations:

    * Method inlining
    * Dead code elimination
    * Loop unrolling

* **Garbage Collector (GC)**

  * Frees memory by removing unused objects from the heap.
  * Runs automatically, but developers can hint with `System.gc()`.
  * Algorithms: Serial GC, Parallel GC, G1 GC, ZGC (latest).

---

### 4. **Native Method Interface (JNI)**

A **bridge** between Java and other languages (e.g., C/C++).

* Allows Java to use OS-level libraries or third-party native libraries.
* Example:

  ```java
  System.loadLibrary("cryptoLib");
  ```

---

### 5. **Native Method Libraries**

The actual libraries (e.g., `.dll` on Windows, `.so` on Linux) used by JNI.

* Provide access to system hardware, file handling, and other low-level operations.

---

## 📊 JVM Components Diagram

```
                 ┌──────────────────────────┐
                 │      Class Loader        │
                 └───────────┬──────────────┘
                             │
          ┌──────────────────┴──────────────────┐
          │         Runtime Data Areas          │
          │  ┌─────────────┬──────────────┐     │
          │  │ Method Area │   Heap        │     │
          │  └─────────────┴──────────────┘     │
          │  ┌─────────────┬──────────────┐     │
          │  │ JVM Stacks  │ PC Registers │     │
          │  └─────────────┴──────────────┘     │
          │          Native Method Stacks       │
          └──────────────────┬──────────────────┘
                             │
                 ┌───────────┴──────────────┐
                 │     Execution Engine     │
                 │  (Interpreter + JIT + GC)│
                 └───────────┬──────────────┘
                             │
                 ┌───────────┴──────────────┐
                 │   Native Method Interface│
                 └───────────┬──────────────┘
                             │
                 ┌───────────┴──────────────┐
                 │   Native Libraries       │
                 └──────────────────────────┘
```

---

## ⚡ Example Walkthrough – JVM in Action

Program:

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, JVM!");
    }
}
```

**Step-by-step inside JVM:**

1. Class Loader loads `HelloWorld.class`.
2. JVM verifies the bytecode.
3. Memory allocated in **Method Area** (class info), **Heap** (objects), **Stack** (method frames).
4. Execution Engine interprets bytecode; JIT may compile frequent parts to native code.
5. Core library (`java.lang.System`) is called.
6. Output → `"Hello, JVM!"`

---

## 📌 Summary

* **JVM = Class Loader + Runtime Data Areas + Execution Engine + JNI + Native Libraries**
* JVM provides **platform independence** by executing bytecode in a virtualized environment.
* Handles memory, execution, security, and native integration.

---
