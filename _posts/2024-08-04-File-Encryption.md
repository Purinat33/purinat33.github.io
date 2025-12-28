---
title: "File Encryption Tool (GUI + scrypt + AES-GCM)"
categories: [Cybersecurity, Encryption]
toc: true
pinned: true
---

# File Encryption Tool (GUI) — Password-Based Encryption with scrypt + AES-GCM

This project is a desktop application that encrypts and decrypts files using a user password. It uses **scrypt** for key derivation and **AES-GCM** for authenticated encryption. The goal is a practical, local tool that is easy to use while following modern cryptography patterns.

---

## What I built

### Core features

- **Encrypt / Decrypt from a GUI** (Tkinter multi-page app)
- **Password-based key derivation** using **scrypt**
- **Authenticated encryption** using **AES-GCM** (confidentiality + tamper detection)
- Stores encryption metadata in a lightweight “authentication table” (`authenticate.csv`)
- Stores ciphertext in a separate `.enc` file (instead of bloating the CSV)

---

## Why AES-GCM + scrypt

### scrypt (KDF)

User passwords are not suitable as encryption keys directly. This project derives a key from the password using **scrypt**, which is designed to be expensive to brute-force.

Parameters used in the project:

- `N = 2^16`, `r = 8`, `p = 1`

These parameters control the cost of key derivation. Higher values increase brute-force resistance at the cost of slower encryption/decryption.

### AES-GCM (AEAD)

AES-GCM is an **AEAD** mode: it provides both:

- **confidentiality** (encryption)
- **integrity/authenticity** (a tag that detects wrong passwords or tampered ciphertext)

If the password is incorrect or the ciphertext is modified, decryption fails during tag verification.

---

## Data storage design

To keep the encrypted data and metadata organized, the app maintains:

### 1) Ciphertext file

- Stored in `./encrypted/`
- Written as base64 text to a file ending in `.enc`

### 2) Authentication table (`authenticate.csv`)

This CSV acts like a tiny database index for encrypted files.

It stores:

- KDF information (algorithm + parameters)
- cryptographic salt
- AES-GCM nonce + tag
- a reference path to the ciphertext file
- the original file path/name (for tracking)

**No plaintext password is stored.**

Example schema created by the program:

```python
fields = ['kdf', 'n', 'r', 'p', 'salt',
          'aead', 'nonce', 'ciphertext_enc', 'tag', 'filename']
```

> Security note: the CSV contains metadata (including original file path). In a real product, you might avoid storing absolute paths or store only a safe identifier.

---

## Encryption workflow

1. User selects a file in the GUI
2. User enters a password
3. App generates:

   - random **salt**
   - derived key using **scrypt(password, salt, N, r, p)**

4. App creates an AES-GCM cipher which generates a **nonce**
5. App encrypts file bytes and produces:

   - **ciphertext**
   - **tag**

6. App writes ciphertext into `./encrypted/<filename>_encrypted.enc`
7. App writes metadata into `authenticate.csv`

Key implementation excerpt (concept):

```python
salt = get_random_bytes(16)
key = scrypt(password, salt, 16, N=N, r=r, p=p)
cipher = AES.new(key, AES.MODE_GCM)
ciphertext, tag = cipher.encrypt_and_digest(file_bytes)
```

---

## Decryption workflow

1. User selects an `.enc` file
2. App looks up the file in `authenticate.csv` and retrieves:

   - scrypt parameters + salt
   - AES-GCM nonce + tag
   - ciphertext file path

3. App derives the key again using the password and stored KDF parameters
4. App decrypts ciphertext and verifies integrity using the tag:

   - if verification fails → wrong password or corrupted ciphertext

5. App writes plaintext into `./decrypted/`

Key excerpt:

```python
key = scrypt(password, salt, 16, N=nn, r=rr, p=pp)
cipher = AES.new(key, AES.MODE_GCM, nonce=nonce)
plaintext = cipher.decrypt(ciphertext)
cipher.verify(tag)   # raises if password wrong or data modified
```

---

## GUI design (Tkinter)

The application uses a simple multi-page layout:

- Start page: choose Encrypt or Decrypt
- Encrypt page: pick a file + enter password + encrypt
- Decrypt page: pick an `.enc` + enter password + decrypt

The GUI provides user feedback through status text and message dialogs:

- “Encryption success” shows output path
- “Decryption success” confirms authenticity
- Errors are shown clearly (e.g., wrong password / corrupted data)

---

## Threat model and limitations

### What it protects against

- Someone opening or reading your file contents without the password
- Basic tampering (AES-GCM detects changes via tag verification)

### What it does not protect against

- Weak passwords (dictionary/brute-force attacks remain possible)
- A compromised machine (keylogger/malware)
- Metadata leakage (file paths and filenames may reveal information)

---

## Improvements I would make next

- **Use a single self-contained encrypted file format**

  - store salt/nonce/tag/ciphertext together in one `.enc` file
  - removes the need for `authenticate.csv`

- **Increase key length to 32 bytes (AES-256)**

  - `scrypt(..., key_len=32, ...)`

- **Add AAD (Associated Data)**

  - bind metadata (like filename) to the ciphertext authenticity check

- **Handle duplicates more robustly**

  - use a unique ID per encryption rather than filename-based checks

- **Packaging**

  - build an executable (PyInstaller) for easier distribution

---

## Repo

- [GitHub Repo](https://github.com/Purinat33/File-Encrypt-and-Decrypt)
