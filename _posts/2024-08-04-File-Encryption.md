---
title: "File Encryption Technique"
categories: [Cybersecurity, Encryption]
---

# Building a Secure File Encryption and Decryption System

## Introduction

In the realm of cybersecurity, file encryption is one of the most effective methods to protect sensitive information. Encryption ensures that only authorized individuals can access the content, maintaining confidentiality and data integrity. This article presents a personal cryptography project that involves building a file encryption and decryption tool using the `cryptography` library in Python.

## Objective

The project aims to develop a command-line utility that allows users to:

- Encrypt text files using a password.
- Decrypt previously encrypted files using the correct password.
- Ensure secure key derivation using PBKDF2.

## Technologies Used

We utilized the following tools and libraries:

- **Python**: The primary programming language for development.
- **cryptography**: A robust cryptographic library that provides secure implementations of encryption algorithms.
- **PBKDF2HMAC**: A key derivation function (KDF) used to generate secure keys from passwords.
- **Fernet**: A symmetric encryption method that ensures confidentiality.
- **dotenv**: To store and load salt securely from an environment file.

## Key Features

### Password-Based Encryption

The encryption mechanism uses **PBKDF2HMAC** to derive a key from the user's password. A random **salt** is used to add uniqueness and protect against precomputed attacks.

### Fernet Encryption

The derived key is used with **Fernet encryption**, ensuring that the data remains secure during encryption and decryption operations.

### Empty Line Handling

The system processes files while stripping unnecessary empty lines, ensuring data integrity in storage and retrieval.

## Implementation Details

### Key Derivation

To generate a strong encryption key, we use the following function:

```python
from cryptography.hazmat.primitives.kdf.pbkdf2 import PBKDF2HMAC
from cryptography.hazmat.primitives import hashes
import base64
from cryptography.fernet import Fernet

def create_key(password: str, salt) -> Fernet:
    """Generates a Fernet key from a password and salt."""
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

### File Encryption

The encryption process involves reading a file, stripping empty lines, and writing the encrypted content:

```python
def encrypt_file(file_name, encrypted_file_name, password, salt):
    with open(file_name, 'r') as f:
        data = f.readlines()

    encryption_key = create_key(password=password, salt=salt)
    encrypted_data = [encryption_key.encrypt(line.encode()).decode() + '\n' for line in data if line.strip()]

    with open(encrypted_file_name, 'w') as f:
        f.writelines(encrypted_data)
```

### File Decryption

The decryption process ensures only valid encrypted content is processed, preventing errors due to invalid keys:

```python
def decrypt_file(encrypted_file_name, password, decrypted_file_name, salt):
    with open(encrypted_file_name, 'r') as f:
        data = [line.strip() for line in f.readlines()]

    decryption_key = create_key(password=password, salt=salt)

    decrypted = []
    for line in data:
        try:
            decrypted.append(decryption_key.decrypt(line.encode()).decode())
        except:
            decrypted.append("ERROR, SKIPPING...\n")

    with open(decrypted_file_name, 'w') as f:
        f.writelines(decrypted)
```

### Secure Salt Management

The system retrieves a pre-defined **salt** from an environment variable to maintain consistency and security:

```python
import os
from dotenv import load_dotenv

load_dotenv()
salt = os.getenv('SALT').encode()
```

## Challenges and Considerations

1. **Secure Key Storage**: The salt must be stored securely to ensure key derivation consistency.
2. **Handling Incorrect Passwords**: An incorrect password should not reveal whether decryption failed due to incorrect credentials or corrupted data.
3. **Avoiding Empty Lines**: The system ensures that empty lines do not interfere with encryption and decryption.

## Conclusion

This project demonstrates practical cryptography implementation using Python. By leveraging **PBKDF2HMAC** and **Fernet encryption**, we ensure secure file protection with password-based authentication. Future enhancements could include:

- Implementing a graphical user interface (GUI) for ease of use.
- Adding support for different encryption algorithms.
- Secure storage of salt and keys using hardware security modules (HSMs) or key management services.

By building this encryption tool, we reinforce our understanding of cryptographic principles and showcase our ability to develop secure software solutions.

---

_This project was built as part of our personal cybersecurity initiatives, highlighting secure coding practices and encryption techniques._
