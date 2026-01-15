#
Private key cryptography: confidentiality.
asymmetric cryptography: authentication, authenticity, and integrity. 
- [Cryptography, Cryptanalysis]

#
- DES, AES
- RSA, ECC
- Diffie-Hellman
- SSH
- SSL/TKS
- PGP and GPG

# 
- Perfect Good Privacy (PGP), OpenPGP, GPG
**PGP** (Pretty Good Privacy) and **GPG** (GNU Privacy Guard) are the gold standards for securing emails and files. They are essentially two versions of the same thing: a suite of tools that combine **Asymmetric encryption**, **Symmetric encryption**, and **Digital signatures** into one seamless package.

## 2. How it Works (The "Hybrid" Process)

PGP is famous for its "Hybrid" approach, which solves the speed problem of RSA and the key-exchange problem of AES that we discussed earlier.

### When you send an encrypted message:

1. **Compression:** PGP compresses the data (making it harder for crackers to find patterns).
2. **Session Key Generation:** It creates a **one-time-use** symmetric key (like an AES key).
3. **Symmetric Encryption:** It encrypts the message with that session key.
4. **Asymmetric Encryption:** It encrypts the *session key* using the **Recipient's Public Key**.
5. **Transmission:** The encrypted message and the encrypted session key are sent together.
