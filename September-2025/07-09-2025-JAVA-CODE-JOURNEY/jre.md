# 📘 Java Runtime Environment (JRE) – Documentation

## 🔹 What is JRE?

The **Java Runtime Environment (JRE)** is a **software package** that provides the minimum environment required to **run Java applications**.

* It does **not** contain development tools like compiler or debugger (those are in the JDK).
* It includes the **JVM** and essential **class libraries** that Java programs need at runtime.

👉 If you just want to *execute* Java code, **JRE is enough**.
👉 If you want to *develop* Java code, you need the **JDK**.

---

## 🏗 JRE Structure

```
JRE
 ├── JVM (Java Virtual Machine)
 │     ├── Class Loader
 │     ├── Runtime Data Areas
 │     ├── Execution Engine (Interpreter + JIT + GC)
 │     └── Native Interfaces
 ├── Core Libraries (java.lang, java.util, java.io, etc.)
 └── Supporting Files & Resources
```

---

## 🔹 Components of JRE

### 1. **Java Virtual Machine (JVM)**

* The engine that runs Java **bytecode**.
* Key parts of JVM inside JRE:

  * **Class Loader** → Loads classes dynamically.
  * **Runtime Data Areas** → Memory model (Heap, Stack, PC register, Method Area).
  * **Execution Engine** → Interpreter + JIT Compiler + Garbage Collector.
  * **Native Method Interface (JNI)** → Bridge between Java and native libraries.

---

### 2. **Core Libraries (Java APIs)**

* Pre-built libraries required for Java programs.
* Examples:

  * `java.lang` → Core classes (String, Object, Math, System).
  * `java.util` → Collections (List, Map, Set).
  * `java.io` → Input/output (files, streams).
  * `java.net` → Networking.
  * `java.sql` → Database connectivity.

---

### 3. **Supporting Files**

* Configuration files and runtime resources.
* In older JDKs, all core classes were bundled in `rt.jar`.
* Since Java 9+, modularized (`java.base`, `java.xml`, etc.).

---

## 🔹 Workflow Example

Suppose we have a compiled class `HelloWorld.class` (bytecode).

1. User runs → `java HelloWorld`
2. **JVM (from JRE)** loads `HelloWorld.class` into memory.
3. Class Loader → Loads the required classes.
4. Execution Engine → Runs bytecode using Interpreter/JIT.
5. Core Libraries (e.g., `System.out.println`) are used.
6. Output is displayed → `Hello, Java World!`

👉 Notice that compilation step (`javac`) is **not possible** with only JRE.

---

## 📌 Summary

* **JRE = JVM + Core Libraries + Supporting Files**
* Provides an environment to **run** Java applications.
* Does **not** include development tools like `javac`, `jdb`, `javadoc`.
* Suitable for end-users who only need to execute Java apps, not develop them.

---
