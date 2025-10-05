# “How is the OS initially installed on a *new* computer at the manufacturing stage?”

---

## 🏭 How OS Is Installed During Manufacturing (Factory Installation Process)

![Os installation](/images/September-2025/24-09-2025/Os%20installation.gif)

When a company like **Dell, HP, Lenovo, or Apple** builds a computer, they don’t manually install the OS one by one like we do.
Instead, they use **automated, large-scale imaging and provisioning systems**.

Let’s break it down step-by-step 👇

---

### ⚙️ 1. **Hardware Assembly**

* The laptop/PC is built with all its components (CPU, SSD/HDD, RAM, etc.).
* At this point, the storage device (SSD/HDD) is **blank** — no OS yet.

---

### 🧠 2. **Connecting to a Factory Server**

* The computer is connected to a **factory network** or **imaging station**.
* It boots into a **pre-installation environment (PE)** — like a minimal OS stored on a local network, PXE server, or USB stick.

📌 This environment allows automation scripts to run (no user needed).

---

### 💽 3. **OS Image Deployment**

* Instead of installing from scratch, manufacturers use a **disk image** — a preconfigured copy of an OS that includes:

  * OS files (Windows/Linux/macOS)
  * Preinstalled drivers
  * Company branding, wallpapers, and tools
  * Sometimes trial software or recovery tools

🧩 Example:
A “master image” is created once and **cloned** onto thousands of devices.

Tools used:

* **Microsoft Deployment Toolkit (MDT)**
* **Windows Preinstallation Environment (WinPE)**
* **PXE Boot servers**
* **Clonezilla**, **Norton Ghost**, or **Acronis**

---

### ⚡ 4. **Automation with Scripts**

* Automation scripts (like Windows **Sysprep**) prepare the OS to be hardware-independent.
* When the image is deployed, the script customizes:

  * Device drivers
  * Serial number, activation key
  * Language, region, time zone
  * OEM logos and recovery partitions

---

### 🧰 5. **Driver Injection**

* Based on hardware configuration, correct **drivers** (for Wi-Fi, display, chipset, etc.) are installed automatically.
* This ensures that when the customer powers on the laptop, everything works out of the box.

---

### 🧾 6. **Testing & Quality Check**

* Automated tests ensure the OS boots properly.
* Sometimes the system is powered on and verified using scripts that check CPU, RAM, SSD, and display.

---

### 🧳 7. **Sealing and Delivery Mode**

* Once testing is complete, the OS enters **OOBE (Out-of-Box Experience)** mode — the screen you see when you first boot your laptop (“Hi there! Let’s get started…”).
* The factory seals the machine, ready for shipping.

---

### 📦 8. **First Boot (User Stage)**

* When you first turn on the laptop, you go through setup:

  * Choose language, connect Wi-Fi, sign in
* Internally, that’s the “final activation” of the preinstalled OS image.

---

## 🖼️ Summary Flow

```
Hardware Assembled
   ↓
Connect to Factory Network
   ↓
Boot into Preinstallation Environment (WinPE/PXE)
   ↓
Deploy Master OS Image
   ↓
Inject Drivers + OEM Customization
   ↓
Automated Testing
   ↓
Set OOBE Mode
   ↓
Package & Ship to Customer 🚚
   ↓
User Powers On → Finishes Setup 🎉
```

---

## 💡 Analogy

Imagine the OS image as a **template cake batter** baked in bulk.
Every laptop just gets the batter poured into a mold (the SSD), then gets frosting (branding, drivers) before being boxed and shipped 🍰.

