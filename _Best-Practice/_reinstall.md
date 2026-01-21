### 1. The Timing
* **Step 1:** Plug in your USB and turn on the computer.
* **Step 2:** Enter your **Boot Menu** (usually F12, F11, or Esc) and select the USB drive.
* **Step 3:** Wait for the "Windows Setup" screen to appear (the one where you choose your Language and Time). **Stop here.**

### 2. Opening the Command Prompt
While you are looking at that purple/blue "Windows Setup" screen:
1. Press **`Shift + F10`** on your keyboard.
* *Note: If you are on a laptop, you might need to press **`Shift + Fn + F10`**.*
2. A black window (Command Prompt) will pop up over the setup screen.

### 3. The Commands (Step-by-Step)
Now, type these exact commands inside that black window:
1. `diskpart` (Press Enter)
2. `list disk` (Press Enter) — *Look for your main drive (usually **Disk 0**).*
3. `select disk 0` (Press Enter) — *Ensure it says "Disk 0 is now the selected disk."*
4. `clean all` (Press Enter) — **This is the wipe.** It takes less than a second. It will say "DiskPart succeeded in cleaning the disk." [ALL] Specifies that each and every byte\sector on the disk is set to zero, which completely deletes all data contained on the disk.
5. `exit` (Press Enter)
6. `exit` (Press Enter) — *This closes the black window.*

### 4. Completing the Reinstall

1. Click **Next** and then **Install Now**.
2. Choose "I don't have a product key" (Windows will auto-activate later).
3. Select **"Custom: Install Windows only (advanced)"**.
4. You will now see **Drive 0 Unallocated Space (476.9 GB)**. No partitions, no old files.
5. Just click **Next**. Windows will handle the rest.

**Would you like me to help you identify which key triggers the "Boot Menu" for your specific computer model?**