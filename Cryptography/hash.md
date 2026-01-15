| Use Case            | Recommended Algorithm | Why?                                          |
|---------------------|----------------------|-----------------------------------------------|
| Passwords           | Bcrypt / Argon2      | Needs to be slow to stop hackers.              |
| File Checking       | SHA-256              | Needs to be fast to save time.                 |
| Digital Signatures  | SHA-512              | Needs to be standard and mathematically "tight." |


# 
- know as digest

# HMAC
- Hashe-based Message Authentication Code (HMAC)
- HMAC(K,M) = H((K⊕opad)||H((K⊕ipad)||M))

### 1. Key Transformation (The "Double Lock")

The goal of HMAC is to create an inner hash and an outer hash. To make this secure, the "key" used for the inner hash must be different from the "key" used for the outer hash.

By XORing  with `ipad` and then separately with `opad`, the algorithm effectively creates two "pseudorandom" versions of your password:

* ** (Inner Secret):** Your password modified by `0x36`.
* ** (Outer Secret):** Your password modified by `0x5C`.

If an attacker somehow breaks the inner hash, they still haven't "solved" the outer hash because the key used there is different.

### 2. Preventing "Length-Extension" Attacks

This is the most important technical reason. Many hash functions (like SHA-1 or MD5) are vulnerable to an attack where a hacker can add data to the end of a message and calculate a new valid hash without knowing the secret key.

* If you just did `Hash(Key + Message)`, an attacker could potentially extend it.
* By using the XOR step to create a **nested structure**—`Hash(OuterKey + Hash(InnerKey + Message))`—the XORed keys act as a "wrapper" that makes it mathematically impossible for an attacker to predict how the final hash will change if they modify the message.

### 3. Preserving Entropy (Information Strength)

XOR is used specifically because it is a **reversible, one-to-one mapping**.

* If you "added" the password to the padding, you might get "carry-over" bits that change the values in a way that loses information.
* With XOR, every single bit of your original password () is preserved. If you have the result of , and you know the `ipad`, you can get the exact  back. This ensures that the "uniqueness" of your password is fully injected into the hash calculation.
