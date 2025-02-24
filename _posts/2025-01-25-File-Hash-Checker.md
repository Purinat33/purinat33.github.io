---
title: "File Hashing Viewer"
categories: [Cybersecurity, Encryption]
---

# Building a GUI File Integrity Checker: A Personal Cybersecurity Project  

In the world of cybersecurity, ensuring file integrity is crucial, especially when dealing with sensitive data or verifying software downloads. To enhance my understanding of cryptographic hash functions and develop a practical security tool, I started working on a **GUI File Integrity Checker** application.  

## 🔍 **Project Overview**  
This application provides an easy-to-use graphical interface that allows users to verify the integrity of any file by computing its hash values using **MD5, SHA256, and CRC32**. Additionally, it enables users to manually input expected hash values to check against the computed ones.  

### **Core Features**  
✅ **Compute Hashes:** Reads and displays the **MD5, SHA256, and CRC32** hash of any selected file.  
✅ **Manual Verification:** Users can manually enter a known hash value for each type to verify against the computed result.  
✅ **Integrity Check:** Displays **MATCH** or **NO MATCH** based on whether the entered hash matches the calculated one.  
✅ **User-Friendly GUI:** A simple and intuitive interface, making it accessible for both cybersecurity professionals and everyday users.  

## ⚙️ **How It Works**  
1. **User selects a file** from the system.  
2. The application calculates the **MD5, SHA256, and CRC32** hashes.  
3. If needed, the user **pastes a known hash value** in the respective text boxes.  
4. The tool **compares the values** and indicates if they match or not.  

## 🔒 **Why Is This Important?**  
📌 **File Integrity Verification** – Ensures files have not been tampered with or corrupted.  
📌 **Cybersecurity Awareness** – Highlights the importance of cryptographic hashes in security.  
📌 **Practical Application** – Useful for verifying software downloads, forensic analysis, and digital forensics.  

## 🛠️ **Tech Stack**  
I’m currently working on the implementation using:  
- **Python** (for logic and hash computations)  
- **Tkinter/PyQt** (for GUI)  
- **Hashlib & Binascii** (for hash generation)  

## [Project Repository](https://github.com/Purinat33/File-Hashing-App/blob/master/fileHashCheck.py)

This project has been an excellent way to deepen my understanding of **file integrity mechanisms, hashing algorithms, and GUI development** while building something useful for cybersecurity tasks.  

## Application Interface

![Demo](/assets/img/demo.png)

