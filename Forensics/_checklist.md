## 📂 Forensic Acquisition Checklist (Live Response)

### 1. Preparation & Pre-Capture

* [ ] **Camera/Photo Documentation:** Take a photo of the screen as it was found (to show open windows, wallpaper, and time).
* [ ] **External Storage:** Ensure your USB/SSD is "Clean" (wiped and formatted) and has enough space (at least  the size of the target RAM + Disk).
* [ ] **Write-Protection:** Ensure your forensic toolkit USB is set to "Read-Only" (if it has a physical switch) or use a trusted portable drive.

### 2. Memory Acquisition (The Most Volatile)

* [ ] **Note the System Time:** Record both the computer's displayed time and the actual current time (to check for clock drift).
* [ ] **Execute DumpIt:** Run directly from the USB.
* [ ] **Destination:** Save the `.raw` file directly to your **External USB**, NOT the local C: drive.
* [ ] **Hashing:** Immediately generate an MD5 or SHA-256 hash of the memory dump.
* [ ] **Log:** Record the file name, size, and hash value.

### 3. Storage Acquisition (Disk Imaging)

* [ ] **Launch FTK Imager Lite:** Run from the USB.
* [ ] **Select Source:** Choose "Physical Drive" (usually `\\.\PhysicalDrive0`).
* [ ] **Select Image Type:** Choose **E01 (EnCase Format)**.
* [ ] **Case Metadata:** Fill in the Case Number, Evidence ID, and Examiner Name.
* [ ] **Verification:** Ensure "Verify images after creating" is checked. This ensures the hash of the source matches the hash of the image.

### 4. Post-Acquisition & Chain of Custody

* [ ] **System Shutdown:** Once the live capture is done, shut down the machine (usually by pulling the power plug to prevent shutdown scripts from running, unless encryption is a concern).
* [ ] **Chain of Custody Form:** Record who handled the USB, what was captured, and the exact time it was secured in an evidence bag.
* [ ] **Hardware Check:** Note the Serial Numbers of the RAM sticks and Hard Drives if possible.

---

### Summary of Best Practices for the Investigator:

1. **Don't touch the mouse/keyboard more than necessary.** Every click creates a registry change or a log entry.
2. **Run commands via CLI if possible.** GUIs require more RAM to be "overwritten" to render the windows.
3. **Always Hash.** If the hash doesn't match later, the evidence is useless in court.

---

```powershell
diskpart
list disk
select disk X
attributes disk set readonly
exit

# Reverted
attributes disk clear readonly
```