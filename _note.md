# 
As revealed by researcher Karsten Nohl in his BadUSB paper, exploiting the firmware of external storage devices, such as USB flash drives and even standard-looking device charging cables, presents adversaries with an incredible toolkit. The firmware can be reprogrammed to make the device look like another device class, such as a keyboard. In this case, it could then be used to inject a series of keystrokes upon an attachment or work as a keylogger. The device could also be programmed to act like a network device and corrupt name resolution, redirecting the user to malicious websites.

Another example is the O.MG cable (theverge.com/2019/8/15/20807854/apple-mac-lightning-cable-hack-mike-grover-mg-omg-cables-defcon-cybersecurity), which packs enough processing capability into an ordinary-looking USB-Lightning cable to run an access point and keylogger.

#
GPS signals can be jammed or even spoofed using specialist radio equipment. This might be used to defeat geofencing mechanisms for instance.'

# BT
BlueBorne exploit (armis.com/blueborne), which can compromise any active and unpatched system regardless of **whether discovery** is enabled and w**ithout requiring any user intervention**. There are also vulnerabilities in the authentication schemes of many devices. 

Using a control center toggle may not actually turn off the Bluetooth radio on a mobile device. If there is any doubt about patch status or exposure to vulnerabilities, Bluetooth **should be fully disabled** through device settings.

a discoverable device is vulnerable to bluejacking, a sort of spam where someone sends you an unsolicited text (or picture/video) message or vCard (contact details). This can also be a vector for malware, as demonstrated by the Obad Android Trojan malware (securelist.com/the-most-sophisticated-android-trojan/35929).

Bluesnarfing refers to using an exploit in Bluetooth to steal information from someone else's phone. The exploit (now patched) allows attackers to circumvent the authentication mechanism. Even without an exploit, a short (four-digit) PIN code is vulnerable to brute force password guessing.

Other significant risks come from the device being connected to another device. **A peripheral device with malicious firmware can be used to launch highly effective attacks.** This type of risk has a low likelihood as demanding resources are required to craft such malicious peripherals.

# NFC

Attacks could be developed using vulnerabilities in handling the tag (securityboulevard.com/2019/10/nfc-false-tag-vulnerability-cve-2019-9295). It is also possible to exploit NFC by crafting tags to direct the device browser to a malicious webpage where the attacker could try to exploit vulnerabilities in the browser.

Certain antenna configurations may be able to pick up the RF signals emitted by NFC from several feet away, allowing an attacker to eavesdrop from a more comfortable distance. An attacker with a reader may also be able to skim information from an NFC device in a crowded area, such as a busy train. An attacker may also be able to corrupt data as it is being transferred through a method similar to a DoS attack—by flooding the area with an excess of RF signals to interrupt the transfer.

# Root
be aware that there is the possibility that it can compromise system files and programming interfaces, so that local shell processes, such as Explorer, taskmgr, or tasklist on Windows or ps or top on Linux, plus port scanning tools, such as netstat, **no longer** reveal its presence (at least, if run from the infected machine). A rootkit may also contain tools for cleaning system logs, further concealing its presence.

There are also examples of rootkits that can reside in **firmware** (either the computer firmware or the firmware of any sort of adapter card, hard drive, removable drive, or peripheral device). These can **survive any attempt to remove the rootkit by formatting the drive and reinstalling the OS**. For example, the US intelligence agencies have developed DarkMatter and Quark Matter UEFI rootkits targeting the firmware on Apple Macbook laptops.

# Wireless Replay
Wireless authentication is vulnerable to various types of replay attacks that **aim to capture the hashes** used when a wireless station associates with an access point. Once the hash is captured, it can be subjected to offline brute force and dictionary cracking. A KRACK attack uses a replay mechanism that targets the WPA and WPA2 4-way handshake. KRACK is effective regardless of whether the authentication mechanism is personal or enterprise. It is important to ensure both clients and access points are fully patched against such attacks.

# Tactic
new attack that the attacker could launch through information they have gained about the network.
attacker may trigger a denial of service attack to divert the security team's attention and then attempt to accelerate plans to achieve actions on objectives