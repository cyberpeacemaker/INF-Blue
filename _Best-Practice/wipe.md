
1. **Power On:** Immediately enter the **BIOS/UEFI settings** (usually by tapping F2, F12, or Del).
2. **Boot Priority:** Change the order so the **USB/Optical Drive** is #1 and the **Infected Hard Drive** is #2 (or disabled entirely).

1. Boot from Clean USB.
2. Open CMD. `Shift + F10`
3. Use **`diskpart`** $\rightarrow$ `clean all` to wipe the entire disk (including MBR/GPT).
4. Reinstall Windows (which automatically creates a new, clean MBR/GPT).
5. If the boot still fails, use **`bootrec`** to troubleshoot the new installation.

---

To conduct the wipe correctly and bypass that "activation," you must change **where the computer looks for instructions** the moment you press the power button.

---

### 1. Changing the Boot Order (Bypassing the MBR)

The "trick" is to prevent the CPU from ever reading the infected MBR.

1. **Power On:** Immediately enter the **BIOS/UEFI settings** (usually by tapping F2, F12, or Del).
2. **Boot Priority:** Change the order so the **USB/Optical Drive** is #1 and the **Infected Hard Drive** is #2 (or disabled entirely).
3. **The Result:** The computer loads the "Clean" Operating System from your USB into RAM. Since the infected hard drive was never "executed," the rootkit code is just **sitting there as "dead" data**. It is never activated, and its Ring 0 drivers are never loaded.

---

### 2. Manually Wiping the MBR/VBR via Command Line

Once you have booted into your clean Live Environment (like a Windows Install USB or Linux Live), you can target the boot sectors specifically.

#### On Windows (via Recovery Command Prompt):

You use the `bootrec` tool to overwrite the malicious boot code with "clean" generic code.

```cmd
bootrec /fixmbr    # Overwrites the Master Boot Record
bootrec /fixboot   # Overwrites the Volume Boot Record (Partition boot sector)

```

#### On Linux (The "Nuclear" Option):

If you want to be 100% sure the partition table and MBR are gone, you can "zero out" the first few hundred bytes of the drive. The MBR lives in the **first 512 bytes**.

```bash
# Overwrite only the first 446 bytes (the executable code part of the MBR)
dd if=/dev/zero of=/dev/sda bs=446 count=1

# Overwrite the entire first 512 bytes (MBR + Partition Table)
dd if=/dev/zero of=/dev/sda bs=512 count=1

```

*Note: After running this, the disk will appear "uninitialized" or "raw" to any installer, because you've deleted the map of where the partitions used to be.*

---

### 3. The Modern Version: UEFI and the ESP

Most modern computers use **UEFI** instead of the old BIOS/MBR style. In this case, the rootkit doesn't hide in a "sector"; it hides as an `.efi` file in a hidden partition called the **EFI System Partition (ESP)**.

* **To wipe this:** You must use a partitioning tool (like `diskpart` on Windows or `gdisk` on Linux) to **delete the entire ESP partition**.
* **CEH Tip:** Even if you delete the partition, some "UEFI Rootkits" (like LoJax) hide in the motherboard's **SPI Flash memory**. This is why the CEH mentions that for the most advanced rootkits, you must also **re-flash the BIOS/UEFI firmware**.

---

Both `bootrec` and `diskpart` are essential command-line tools for a CEH or systems administrator when dealing with corrupted or infected systems. However, they are used at different stages of the remediation process.

---

## 1. The `bootrec` Tool

`bootrec.exe` is a specialized tool used to repair the **Master Boot Record (MBR)**, **Boot Sector**, and **Boot Configuration Data (BCD)**.

### How to reach it:

You **cannot** run `bootrec` from a normal Windows CMD while the OS is running. If you try, you will get an error.

* **Why?** It requires exclusive access to the boot files which are "locked" while Windows is active.
* **The Access Path:** You must boot from a **Windows Installation Media (USB/DVD)** $\rightarrow$ Select **Repair your computer** $\rightarrow$ **Troubleshoot** $\rightarrow$ **Advanced Options** $\rightarrow$ **Command Prompt**.

### The 4 Key Commands:

| Command | What it does | CEH Context |
| --- | --- | --- |
| `bootrec /fixmbr` | Writes a standard Windows MBR to the first sector of the disk. | **Cleans out MBR-level Rootkits** by overwriting their malicious code. |
| `bootrec /fixboot` | Writes a new boot sector to the system partition. | Fixes VBR (Volume Boot Record) corruption. |
| `bootrec /scanos` | Scans all disks for Windows installations not currently in the BCD. | Useful if a rootkit "hid" an OS partition. |
| `bootrec /rebuildbcd` | Completely rebuilds the boot menu. | Essential if a rootkit corrupted the boot configuration to stay persistent. |

---

## 2. The `diskpart` Tool

`diskpart` is the Windows command-line utility for disk partitioning. Unlike `bootrec`, you **can** reach this from a normal CMD (with Admin rights), but for rootkit remediation, you should run it from the **Recovery Environment** to ensure the disk isn't "protected" by the malware.

### Why use `diskpart` for a Rootkit Wipe?

While `bootrec` fixes the *code* that starts the computer, `diskpart` is used to destroy the **partitions** where the malware lives.

### The "Clean" Command (The "Nuclear" Option)

In the CEH workflow for a full system wipe, the `clean` command is the most important:

1. Type `diskpart`
2. `list disk` (Identify your drive, e.g., Disk 0)
3. `select disk 0`
4. `clean` (or `clean all`)

**The Difference:**

* `clean`: Deletes the partition table (MBR/GPT) only. The data is still there, but the OS can't find it.
* `clean all`: Overwrites every sector of the disk with zeros. **This is the actual "Full System Wipe" recommended for Kernel-mode rootkits.** It takes much longer but ensures total remediation.

---

## 3. Comparison for Remediation

| Tool | Focus | Use Case |
| --- | --- | --- |
| **bootrec** | **The "Engine Start"** | Fixing the boot sequence without necessarily deleting your data. |
| **diskpart** | **The "Plot of Land"** | Preparing, wiping, or restructuring the physical storage of the drive. |

### The CEH Remediation Flow:

If you are performing the "Full System Wipe" mentioned in your previous question, the flow is:

1. Boot from Clean USB.
2. Open CMD.
3. Use **`diskpart`** $\rightarrow$ `clean all` to wipe the entire disk (including MBR/GPT).
4. Reinstall Windows (which automatically creates a new, clean MBR/GPT).
5. If the boot still fails, use **`bootrec`** to troubleshoot the new installation.
