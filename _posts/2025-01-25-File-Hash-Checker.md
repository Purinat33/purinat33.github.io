---
title: "File Hashing Viewer"
categories: [Cybersecurity, Encryption]
toc: true
---

# File Hashing Viewer — GUI File Integrity Checker

A simple desktop application that computes **MD5**, **SHA-256**, and **CRC32** hashes for any selected file and helps users verify integrity by comparing against known hash values (e.g., from a software publisher).

- Repo: https://github.com/Purinat33/File-Hashing-App/blob/master/fileHashCheck.py

![Demo](/assets/img/demo.png){: .shadow w="900" }

---

## What this tool does

### Core features

- **Compute hashes** for a selected file: MD5, SHA-256, CRC32
- **Manual verification**: paste an expected hash and compare
- **Clear results**: shows **MATCH** / **NO MATCH** for each algorithm
- **GUI-first workflow**: designed for quick checks without using a terminal

---

## Why file hashing matters

Hashing is a practical way to detect accidental changes (corruption) and many forms of tampering. Common real-world use cases include:

- verifying downloaded installers against official checksums
- confirming files weren’t modified during transfer
- basic forensic workflows (cataloging and checking evidence files)

---

## Threat model and limitations (important)

This tool is useful for **integrity checking**, but the strength depends on the hash type and the threat:

- **SHA-256** is the primary integrity check for security-sensitive use.
- **MD5** is fast and widely used historically, but it is **not collision-resistant** and should not be relied on for adversarial tampering.
- **CRC32** is mainly for **error detection** (accidental corruption), not cryptographic security.

> Practical recommendation: if a publisher provides multiple hashes, prefer **SHA-256**. Use MD5/CRC32 for convenience or non-adversarial checks.

---

## How it works

1. User selects a file
2. The app reads the file and computes:
   - MD5 (hashlib)
   - SHA-256 (hashlib)
   - CRC32 (binascii / zlib)
3. User optionally pastes expected hashes into the UI
4. The app compares and displays **MATCH / NO MATCH**

---

## Implementation notes

- **Language:** Python
- **GUI:** Tkinter (or PyQt, depending on your final choice)
- **Hashing:** `hashlib` for MD5/SHA-256 and `binascii`/`zlib` for CRC32

### Design choice: chunked file reading (recommended if present)

To support large files without high memory usage, hashes should be computed by reading the file in chunks (instead of loading it all at once). If your code already does this, call it out here—it’s a strong engineering detail.

---

## Future improvements

- Add **copy-to-clipboard** buttons for each hash
- Add drag-and-drop file selection
- Add additional algorithms (SHA-1 for legacy checks, SHA-512, BLAKE2)
- Add an “Export report” option (file path, size, hashes, timestamp)
- Optional: verify hashes from a checksum file format (e.g., `.sha256`)

---
