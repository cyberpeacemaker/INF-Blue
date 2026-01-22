Below are **practical, Windows-11–specific ways to disable UPnP and mDNS**, from easiest to most thorough. You can do all of them, or just the ones that fit your threat model.

---

## 1️⃣ Disable UPnP in Windows 11

UPnP in Windows is mainly provided by **two services**.

### A. Disable UPnP services (recommended)

1. Press **Win + R** → type `services.msc`
2. Disable these **two services**:

   * **UPnP Device Host**
   * **SSDP Discovery**

For each:

* Double-click
* **Startup type:** `Disabled`
* Click **Stop**
* Apply → OK

🔒 This stops UPnP entirely at the OS level.

---

### B. Block UPnP via Windows Firewall (extra hardening)

UPnP uses **UDP 1900** (SSDP).

1. Open **Windows Defender Firewall with Advanced Security**
2. Go to **Inbound Rules**
3. Create **New Rule**

   * Type: `Port`
   * Protocol: `UDP`
   * Port: `1900`
   * Action: `Block`
   * Profile: All
   * Name: `Block UPnP SSDP`

(Optional) Repeat for **Outbound Rules**.

---

### C. Disable UPnP on your router (VERY important)

Even if Windows disables UPnP, your **router may still expose it**.

* Log into router (usually `192.168.1.1`)
* Disable **UPnP / NAT-PMP**
* Save & reboot

🛑 This is critical if you care about LAN security.

---

## 2️⃣ Disable mDNS (Multicast DNS) in Windows 11

Windows uses mDNS primarily via **Bonjour** or **network discovery features**.

### A. Disable Network Discovery (simple method)

1. Open **Settings**
2. Go to **Network & Internet → Advanced network settings**
3. Click **Advanced sharing settings**
4. Turn **OFF**:

   * Network discovery
   * File and printer sharing

This stops most mDNS announcements.

---

### B. Disable mDNS via Services (Bonjour)

If installed (common with iTunes, Adobe, etc.):

1. Open `services.msc`
2. Find **Bonjour Service**
3. Set:

   * Startup type: `Disabled`
   * Stop the service

---

### C. Block mDNS via Firewall (most effective)

mDNS uses **UDP 5353 (multicast)**.

#### Block inbound mDNS:

1. Firewall → Advanced Settings
2. **Inbound Rules → New Rule**
3. Type: `Port`
4. Protocol: `UDP`
5. Port: `5353`
6. Action: `Block`
7. Profiles: All
8. Name: `Block mDNS Inbound`

#### Block outbound mDNS:

Repeat for **Outbound Rules**

---

## 3️⃣ Disable Related Discovery Services (Optional but recommended)

In `services.msc`, disable:

| Service                                 | Recommendation |
| --------------------------------------- | -------------- |
| Function Discovery Provider Host        | Disable        |
| Function Discovery Resource Publication | Disable        |
| SSDP Discovery                          | Disable        |
| UPnP Device Host                        | Disable        |

⚠️ Note: This breaks **automatic device discovery** (printers, smart TVs).

---

## 4️⃣ Verify They Are Disabled

### Check listening ports:

```powershell
netstat -an | findstr ":1900"
netstat -an | findstr ":5353"
```

No output = success.

---

## 5️⃣ Security Impact Summary

| Feature           | Risk                           | Safe to Disable? |
| ----------------- | ------------------------------ | ---------------- |
| UPnP              | High (LAN & NAT exposure)      | ✅ Yes            |
| mDNS              | Medium (device fingerprinting) | ✅ Yes            |
| Network Discovery | Low–Medium                     | ✅ If not needed  |

---

## ⚠️ What Will Break?

* Auto printer discovery
* Smart TV / Chromecast discovery
* AirPlay / Apple device discovery
* Some enterprise discovery tools

---

If you want, I can:

* Provide **PowerShell scripts** to disable everything automatically
* Give a **Windows hardening checklist**
* Help tailor this for **corporate vs home networks**

Just tell me 👍
