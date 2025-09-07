# 📘 Java Development Kit (JDK) – Documentation

## 🔹 What is JDK?

The **Java Development Kit (JDK)** is a **software development environment** used to build, compile, debug, and run Java applications.

* It is the **complete toolkit for Java developers**.
* It includes the **JRE (Java Runtime Environment)** for running Java programs and additional tools for development.

---

## 🏗 JDK Structure

```
JDK
 ├── JRE (Java Runtime Environment)
 │     ├── JVM (Java Virtual Machine)
 │     └── Core Libraries
 └── Development Tools
```

---

## 🔹 JDK vs JRE vs JVM

| Component | Purpose                                        | Contains                                           |
| --------- | ---------------------------------------------- | -------------------------------------------------- |
| **JDK**   | For **developing + running** Java applications | JRE + Development Tools                            |
| **JRE**   | For **running** Java applications only         | JVM + Core Libraries                               |
| **JVM**   | Executes Java **bytecode**                     | Class Loader, Execution Engine, Runtime Data Areas |

---

## 🔹 Components of JDK

### 1. **Development Tools**

* **Compiler (`javac`)** → Converts `.java` source files into `.class` bytecode files.
* **Interpreter/Launcher (`java`)** → Runs compiled bytecode on the JVM.
* **Debugger (`jdb`)** → Debugs Java programs.
* **Documentation Tool (`javadoc`)** → Generates HTML docs from comments.
* **Archiver (`jar`)** → Packages classes/resources into `.jar` files.
* **Disassembler (`javap`)** → Examines bytecode in `.class` files.
* **Monitoring/Profiling Tools** → `jconsole`, `jvisualvm`, `jstat`, `jmap`, `jstack`.
* **Security Tools** → `keytool`, `jarsigner`.
* **Other Utilities** → `rmic`, `serialver`.

### 2. **JRE (Included in JDK)**

* **JVM** → Runs Java bytecode.
* **Core Libraries** → Pre-built Java classes (e.g., `java.lang`, `java.util`, `java.io`).
* **Supporting Files** → Configuration & resources.

---

## 🔹 Workflow Example

1. Write a program `HelloWorld.java`
2. Compile → `javac HelloWorld.java` → creates `HelloWorld.class`
3. Run → `java HelloWorld` → output displayed

✅ Compilation requires **javac** (from JDK).
✅ Running only needs **JRE**.

---

## 📌 Summary

* **JDK = JRE + Development Tools**
* Needed for **both running and developing** Java applications.
* Provides everything: compiler, debugger, documentation generator, packaging tools, monitoring tools, and security utilities.

---
