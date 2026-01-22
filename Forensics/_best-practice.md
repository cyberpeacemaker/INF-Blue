The correct answer is: **Connect a write blocker to the hard drive. Then, leveraging a forensic workstation, utilize the dd command in a live Linux environment to create a duplicate copy.**

This procedure follows the industry standards for **Digital Forensics and Incident Response (DFIR)** because it ensures that the original evidence remains unchanged and that a bit-for-bit copy is created for analysis.

---

### Why this is the correct forensic approach

* **Write Blocker:** This is the most critical tool. It physically prevents the forensic workstation from writing any data (even metadata like "last accessed" timestamps) to the CEO's hard drive. Without this, the evidence could be ruled inadmissible in court.
* **Bit-for-Bit Copy ():** Unlike a simple "copy-paste," the `$dd$` command creates an exact image of the entire drive, including deleted files, unallocated space, and hidden partitions where malware often hides.
* **Live Linux Environment:** Using a live environment (booting from a USB/CD) ensures that no local operating system processes are running, which prevents the computer from altering its own logs or files during the imaging process.

---

### Why the other options are incorrect:

| Option | Why it fails forensic standards |
| --- | --- |
| **Tamper-evident bag only** | Putting a drive in a bag is part of the **Physical Chain of Custody**, but it doesn't create the **Duplicate Copy** required for the investigation. You still need to image the drive before storing it. |
| **Copy to fileshare with CEO watching** | A "copy-paste" only captures visible files. It misses system metadata, deleted files, and slack space. Furthermore, a fileshare is not a secure forensic container; it can be easily modified later. |
| **Refrain until confirmed** | This is a "wait and see" approach that allows volatile evidence to be lost. In forensics, you image the drive **as soon as possible** to preserve the state of the machine at the time of the suspected incident. |

---

### The Forensic Chain of Custody

Once the image is created using `$dd$`, the analyst must perform these additional steps to ensure the **Chain of Custody**:

1. **Hashing:** Immediately calculate a cryptographic hash (like SHA-256) of both the original drive and the new image. If the hashes match, the copy is verified as identical.
2. **Documentation:** Record exactly who handled the drive, the serial numbers, the time, the date, and the location.
3. **Secure Storage:** Place the original drive in a static-shielded, tamper-evident bag and lock it in a physical safe.

Would you like to know how the **SHA-256 hash** is used later in court to prove that the evidence was never tampered with?