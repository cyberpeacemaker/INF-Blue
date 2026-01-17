## 2. Why STARTTLS is technically "Less Secure"

You are right to be suspicious of STARTTLS. It is vulnerable to a **STRIPTLS** (or "Man-in-the-Middle") attack.

1. **The Client:** "Hey Server, I'd like to use STARTTLS to encrypt our talk."
2. **The Hacker (in the middle):** Intercepts that message and deletes it.
3. **The Server:** Never hears the request and stays in plain text.
4. **The Result:** Your computer thinks the server doesn't support encryption, so it sends your password in **plain text**, which the hacker now has.

With **SMTPS (Port 465)**, this can't happen. If the encryption doesn't start immediately, the connection simply fails. There is no "plain text" fallback.

---

## 3. The 2026 Reality: The "Return to Port 465"

The industry realized you were right. A few years ago, a new standard (**RFC 8314**) was released that essentially said: *"Actually, STARTTLS was a mistake. We should go back to Implicit TLS (SMTPS) for everything."*

| Protocol | Preferred Modern Port | Method | Why? |
| --- | --- | --- | --- |
| **SMTP** | **465** | **Implicit TLS** | Encrypted from second zero. No downgrade attacks. |
| **IMAP** | **993** | **Implicit TLS** | The global standard for years. |
| **POP3** | **995** | **Implicit TLS** | The global standard for years. |

