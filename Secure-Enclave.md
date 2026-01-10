This is the "magic" of the architecture: **The main CPU actually never reads the private key.** Your intuition is correct for normal software: usually, the CPU must load data into RAM to "see" it and use it. However, a Secure Enclave (or TPM) is a **separate computer** with its own CPU and its own private RAM that the main CPU cannot touch.

Here is exactly how the "Challenge" works without the key ever being "read" by the main system:

### 1. The "Black Box" Workflow

Instead of the main CPU doing the math, it acts like a courier. It doesn't know what’s inside the package; it just delivers it.

1. **The Challenge arrives:** The website sends a random "Challenge" string to your browser.
2. **The Hand-off:** Your main CPU (the "Application Processor") takes that challenge and sends it to the **Secure Enclave**.
3. **Inside the Enclave:** The Enclave's **own tiny CPU** wakes up. It pulls the private key from its **own internal, isolated memory** (not your system RAM).
4. **The Signing:** The Enclave CPU performs the math (cryptographic signing) *inside the enclave*.
5. **The Result:** The Enclave sends only the **Digital Signature** back out to the main CPU.

### 2. Why it isn't in System RAM

When you take a memory dump with **DumpIt**, you are capturing the **System RAM** used by Windows and your apps.

* **System RAM:** Contains your browser, your background apps, and the "Digital Signature" (after it's created).
* **Enclave RAM:** This is a tiny, separate physical area of memory located inside the processor chip itself. It is electronically "fenced off." The wires that connect the System RAM to the CPU do not have access to the Enclave's internal memory.

### 3. "Handles" vs. "Keys"

If you were to look at the memory of your browser during this process, you wouldn't find the private key. Instead, you would find a **Key Handle**.

* **The Key:** `0xAbC123...` (The secret, stored only in the Enclave).
* **The Handle:** `ID: 99` (A reference number).

The browser just says to the Enclave: *"Hey, use Key ID 99 to sign this challenge."* The browser never knows what Key 99 actually looks like.
