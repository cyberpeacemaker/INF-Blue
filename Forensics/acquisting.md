- wireshark (> .pcap & .pcapng), procmon
- regshot, memory **dump it**, storage **FTK image** (dd)

- firewall log

# Chain of Custody
documentation records where, when, and who collected the evidence, who subsequently handled it, and where it was stored.
Every person in the chain who handles evidence must log the methods and tools they used.

### The Professional "Live Acquisition" Workflow

#### 1. The "Order of Volatility" (Speed Matters)

You must capture the data that disappears fastest first.

* **Step 1: RAM (DumpIt).** Memory changes every millisecond. You do this before anything else.
* **Step 2: Storage (FTK Imager).** Hard drive data is "persistent" (it stays after power-off), so it is captured after the volatile RAM.

**Hash**

#### 2. Why use a USB? (The "Trusted Toolkit")

You never want to install software on the suspect's computer.

* **Portable Versions:** You use **FTK Imager Lite** and **DumpIt.exe** directly from your USB. This prevents the OS from writing new files to the suspect's hard drive.
* **Saving the Output:** You **never** save the dump files to the suspect's drive. You save them back to your external USB or a high-speed external SSD.

#### 3. "Bit-to-Bit" vs. "Forensic Image"

* **Memory:** When you use DumpIt, it creates a **Raw (.raw/.bin)** file. This is a bit-to-bit copy of the physical RAM.
* **Storage:** When using FTK Imager, you should choose the **E01 (EnCase)** format rather than a raw "dd" bit-to-bit copy for the hard drive.
* **Why E01?** It includes internal compression and, most importantly, **metadata** (case number, examiner name, and automatic hashes) inside the file itself.


---

# Priority
1. CPU **registers** and **cache** memory (including cache on disk controllers, graphics cards, and so on).
2. Contents of nonpersistent system **memor**y (RAM), including routing table, ARP cache, process table, kernel statistics.
3. Data on persistent mass **storage devices** (HDDs, SSDs, and flash memory devices):
    - Partition and file system blocks, slack space, and free space.
    - System memory caches, such as swap space/virtual memory and hibernation files.
    - Temporary file caches, such as the browser cache.
    - User, application, and OS files and directories.
4. Remote logging and monitoring data.
5. Physical configuration and network topology.
6. Archival media and printed documents.

### 1. The Gap Between Theory and Practice

CompTIA wants you to know that **CPU Registers and Cache** are the "most volatile" because they change in nanoseconds. If you lose power, that data is gone instantly.

However, there is no "standard" software tool like DumpIt or FTK Imager that can capture them on a live system because:

* **The "Observer Effect":** To run a tool to capture registers, the CPU must *use* those registers to run the tool itself. This immediately overwrites the evidence you are trying to save.
* **Hardware Isolation:** The operating system (Windows/Linux) generally does not allow software to take a "snapshot" of the physical CPU cache or real-time registers without halting the entire processor.

### 2. How are they "actually" captured?

In high-end labs or specialized malware research, these are only captured using methods that bypass the OS:

* **Hardware Debuggers (JTAG):** Physically connecting a device to the motherboard that can "freeze" the CPU and read the internal states.
* **Virtual Machine Snapshots:** If the target is a VM, the hypervisor (like VMware or Hyper-V) can pause the virtual CPU and save the registers/cache to a `.vmsn` or `.vmss` file.
* **Kernel Debugging:** Using tools like **WinDbg** to "break" the system into a debug state.

### 3. The Forensic Hierarchy (The "Why")

When you see this on your exam, remember that it is about **Priority**, not **Ease of Capture**.

| Evidence Type | Volatility | Practical Tool |
| --- | --- | --- |
| **CPU Registers** | Nanoseconds | Hardware Debugger / VM Snapshot |
| **CPU Cache** | Nanoseconds | Hardware Debugger / VM Snapshot |
| **RAM (System Memory)** | Seconds/Minutes | **DumpIt, Magnet RAM Capture, FTK Imager** |
| **Temp/Swap Files** | Minutes/Hours | Disk Imaging |
| **Hard Drive (SSD/HDD)** | Days/Years | **FTK Imager, EnCase** |
---

# Integrity and Non-Repudiation
Once the target disk has been safely attached to the forensics workstation, data acquisition proceeds as follows:

1. A cryptographic hash of the disk media is made, using either the MD5 or SHA hashing function.
2. A bit-by-bit copy of the media is made using an imaging utility.
3. A second hash is then made of the image, which should match the original hash of the media.
4. A copy is made of the reference image, validated again by the checksum. Analysis is performed on the copy.

This proof of integrity ensures non-repudiation. If the provenance of the evidence is certain, the threat actor identified by analysis of the evidence cannot deny their actions. The hashes prove that no modification has been made to the image.

---

# live snapshot 
(not for forensics, but for debugging process)
## Live snapshot of win11
```powershell
# Get your storage subsystem name
$StorageName = (Get-StorageSubSystem).FriendlyName
# Trigger the live dump
Get-StorageDiagnosticInfo -StorageSubSystemFriendlyName $StorageName -IncludeLiveDump -DestinationPath "$env:USERPROFILE\Desktop\LiveDump"
```

## Dump specific Process
```powershell
# Download Procdump if you don't have it
Invoke-WebRequest "https://live.sysinternals.com/procdump.exe" -OutFile "$env:TEMP\procdump.exe"
# Dump a specific process by name (e.g., lsass)
&$env:TEMP\procdump.exe -ma lsass.exe "$env:USERPROFILE\Desktop\lsass_dump.dmp" -accepteula
```

Before you pull the RAM or the Disk, you should run a "Live Response" script. Tools like Velociraptor or IR-Rescue can grab the ARP Cache and Network Connections instantly. These are often lost or corrupted the second you start a heavy RAM dump like DumpIt.


ir-rescue [https://github.com/diogo-fernan/ir-rescue]


Data Type,Tool to Use,Why?
CPU/Kernel Stats,WinDbg / Memory Dump,"Captured in the ""header"" of a full system dump."
RAM / Processes,DumpIt / Magnet RAM,Creates the raw file for analysis.
ARP / Routing,Velociraptor / Netstat,"Best collected ""live"" before the machine is turned off."
Disk (Full),FTK Imager + Write Blocker,"Captures deleted files and ""slack"" space."
Deleted Files,Autopsy,"""Carves"" the FTK image to find hidden data."
Remote Logs,SIEM (Splunk/Sentinel),Necessary to see what the attacker did before they touched the PC.

---
Method,Output File,Best For...
Live Kernel Dump,LiveDump.dmp,"Seeing the ""Whole Picture"" (Kernel + All Processes)."
ProcDump,process_name.dmp,Deep-diving into a single suspect application.
Task Manager,System.dmp,Quick manual capture of the kernel.
- procdump?

