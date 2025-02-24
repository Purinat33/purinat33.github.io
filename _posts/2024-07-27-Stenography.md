---
title: "Image Hiding using Stenography"
categories: [Cybersecurity, Encryption]
---

# Image Steganography: Hiding Information in Plain Sight

## Introduction
Steganography is the art of hiding information within another medium, making it invisible to unintended observers. Unlike encryption, which scrambles data into an unreadable format, steganography conceals data within seemingly innocuous files—such as images. This project explores a simple yet effective way to embed an image within another image using a graphical user interface (GUI) built with Tkinter and the Python Imaging Library (PIL).

## Project Overview
This project provides a GUI-based tool to perform image steganography, allowing users to:

- **Select a main image**: This image will carry the hidden information.
- **Select a hidden image**: This image will be embedded within the main image.
- **Encrypt the hidden image** into the main image.
- **Decrypt the hidden image** from an encrypted image.

### How It Works

The implementation relies on bit manipulation to store a hidden image within a main image while preserving the visual integrity of the carrier image. The process involves:

#### Encryption Process
1. **Load the main and hidden images** and ensure they are the same dimensions.
2. **Modify the least significant bits (LSBs)** of the main image to store pixel data from the hidden image.
3. **Save the modified image** as the encrypted image, visually similar to the original main image.

#### Decryption Process
1. **Load the encrypted image**.
2. **Extract the LSBs** from each pixel to reconstruct the hidden image.
3. **Save the extracted image**, restoring the original hidden content.

## Implementation
The application is built using Python's Tkinter for the GUI and PIL for image processing. Below is a breakdown of the key components.

### Graphical User Interface (GUI)
The interface provides buttons to upload images, encrypt, and decrypt them. The Tkinter framework ensures user-friendly interaction.

```python
from PIL import Image, ImageTk
import tkinter as tk
from tkinter import filedialog

# Tkinter-based GUI for handling file selection and processing
```

### Encryption Logic
The encryption function manipulates the LSBs of the main image to embed the hidden image while keeping it imperceptible to the human eye.

```python
def encrypt(main_img_path, hidden_img_path, encrypted_img_path, num_bits=2):
    main_img = Image.open(main_img_path).convert("RGB")
    hidden_img = Image.open(hidden_img_path).convert("RGB")
    
    if main_img.size != hidden_img.size:
        raise ValueError("Images must have the same dimensions.")
    
    main_pixels = list(main_img.getdata())
    hidden_pixels = list(hidden_img.getdata())
    
    mask = (1 << num_bits) - 1
    encrypted_pixels = [
        ((m[0] & ~mask) | (h[0] >> (8 - num_bits)),
         (m[1] & ~mask) | (h[1] >> (8 - num_bits)),
         (m[2] & ~mask) | (h[2] >> (8 - num_bits)))
        for m, h in zip(main_pixels, hidden_pixels)
    ]
    
    encrypted_img = Image.new("RGB", main_img.size)
    encrypted_img.putdata(encrypted_pixels)
    encrypted_img.save(encrypted_img_path)
```

### Decryption Logic
Extracts the hidden image by isolating the LSBs of the encrypted image.

```python
def decrypt(encrypted_img_path, decrypted_img_path, num_bits=2):
    encrypted_img = Image.open(encrypted_img_path)
    encrypted_pixels = list(encrypted_img.getdata())
    
    decrypted_pixels = [
        ((p[0] & ((1 << num_bits) - 1)) << (8 - num_bits),
         (p[1] & ((1 << num_bits) - 1)) << (8 - num_bits),
         (p[2] & ((1 << num_bits) - 1)) << (8 - num_bits))
        for p in encrypted_pixels
    ]
    
    decrypted_img = Image.new("RGB", encrypted_img.size)
    decrypted_img.putdata(decrypted_pixels)
    decrypted_img.save(decrypted_img_path)
```

## Importance of Steganography
Steganography has significant applications in:
- **Secure communication**: Concealing messages in images to evade detection.
- **Data integrity verification**: Embedding verification data within images.
- **Digital watermarking**: Protecting intellectual property by hiding ownership information.
- **Forensic analysis**: Steganographic techniques are used for uncovering hidden data in cyber investigations.

## Application of This Project
This project serves as a foundation for:
- **Learning and research** in image processing and cybersecurity.
- **Building secure communication tools** to discreetly transmit information.
- **Enhancing digital watermarking techniques** to prevent data tampering.

## Conclusion
This image steganography project demonstrates a simple yet effective method for hiding information within images. By leveraging Tkinter and PIL, users can encrypt and decrypt hidden images effortlessly. With further improvements, such as password-based encryption or AI-driven enhancements, this tool can be adapted for more advanced applications in security and privacy.

## GitHub Repository: [Click Here](https://github.com/Purinat33/stenography_simple/blob/master/steno.py)
