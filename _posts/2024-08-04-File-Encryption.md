---
title: "File Encryption Technique"
categories: [Cybersecurity, Encryption]
toc: true
---

# File Encryption Technique (Password-Based CLI)

A command-line tool that encrypts and decrypts text files using **password-based key derivation (PBKDF2-HMAC)** and **symmetric authenticated encryption (Fernet)** from Python’s `cryptography` library.

- Repo: https://github.com/Purinat33/File-Cryptography

---

## What this project does

The tool supports two modes:

1. **Encrypt** a text file with a user-provided password
2. **Decrypt** an encrypted file using the correct password

Under the hood:

- A key is derived from the password using **PBKDF2HMAC (SHA-256) + salt**
- The derived key is used with **Fernet**, which provides:
  - confidentiality (data is unreadable without the key)
  - integrity/authenticity (tampering is detected)

---

## Threat model (what it protects against)

This is designed to protect files against:

- casual access (someone opening the file normally)
- accidental leakage (encrypted output is safe to store/share compared to plaintext)

It does **not** protect against:

- weak passwords (dictionary/brute-force becomes feasible)
- attackers who obtain both the encrypted file and a guessable password
- advanced operational security issues (keylogging, malware, compromised machine)

---

## Core design choices

### 1) Password → key (PBKDF2)

Passwords are not used directly as encryption keys. Instead, PBKDF2 “stretches” the password into a strong key.

**Why salt matters**

- Salt prevents attackers from using precomputed rainbow tables across many files/users.
- The salt must be consistent to decrypt later.

In this implementation, salt is loaded from an environment variable.

### 2) Fernet for encryption

Fernet is a high-level, safe primitive for symmetric encryption that includes authentication (so you can detect wrong keys or tampering).

---

## Key implementation excerpts

### Key derivation

```python
def create_key(password: str, salt) -> Fernet:
    bpassword = password.encode()
    kdf = PBKDF2HMAC(
        algorithm=hashes.SHA256(),
        length=32,
        salt=salt,
        iterations=16000
    )
    key = base64.urlsafe_b64encode(kdf.derive(bpassword))
    return Fernet(key=key)
```
````

### File encryption (line-by-line)

```python
def encrypt_file(file_name, encrypted_file_name, password, salt):
    with open(file_name, 'r') as f:
        data = [line for line in f.readlines() if line.strip()]

    fernet = create_key(password=password, salt=salt)
    encrypted_data = [fernet.encrypt(line.encode()).decode() + "\n" for line in data]

    with open(encrypted_file_name, 'w') as f:
        f.writelines(encrypted_data)
```

### File decryption with wrong-key handling

```python
def decrypt_file(encrypted_file_name, password, decrypted_file_name, salt):
    with open(encrypted_file_name, 'r') as f:
        data = [line.strip() for line in f.readlines() if line.strip()]

    fernet = create_key(password=password, salt=salt)

    decrypted = []
    for line in data:
        try:
            decrypted.append(fernet.decrypt(line.encode()).decode())
        except:
            decrypted.append("ERROR, SKIPPING...\n")

    with open(decrypted_file_name, 'w') as f:
        f.writelines(decrypted)
```

---

## Limitations (important)

- **Salt storage:** using a fixed salt from `.env` works for learning, but it’s not ideal for real usage.
- **Text-only behavior:** current approach reads/writes as text and encrypts line-by-line; it’s not suited for binary files.
- **Error handling:** returning `"ERROR, SKIPPING..."` can produce mixed output; a more secure UX is to fail decryption cleanly.

---

## Improvements I would make next

### Store salt with the ciphertext (recommended)

Generate a random salt per encryption, then store it in the encrypted output (header or metadata). That way:

- every encryption is unique
- decryption doesn’t depend on a hidden `.env` value

### Encrypt the full file as bytes

Read the entire file in binary and encrypt once:

- supports any file type
- preserves exact content (including empty lines)

### Increase KDF strength / add memory-hard KDF

- PBKDF2 is common, but modern systems often prefer Argon2/scrypt for better brute-force resistance.
- Also tune iterations higher depending on performance constraints.

### Cleaner failure behavior

When decryption fails, return a single clear error message instead of partial output.

---

## How to run

1. Clone the repo: [https://github.com/Purinat33/File-Cryptography](https://github.com/Purinat33/File-Cryptography)
2. Create a `.env` file with `SALT=...`
3. Place plaintext files in `./input/`
4. Run the script and choose encrypt/decrypt mode
