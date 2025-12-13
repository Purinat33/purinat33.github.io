---
title: "Image Hiding using Stenography"
categories: [Cybersecurity, Encryption]
toc: true
---

# Image Steganography Tool (LSB) — Hide One Image Inside Another

This project is a GUI-based steganography tool that hides a **full RGB image inside another RGB image** using **Least Significant Bit (LSB)** manipulation. The encrypted output looks nearly identical to the carrier image, but can be decoded to recover the hidden image.

- GitHub: https://github.com/Purinat33/stenography_simple/blob/master/steno.py

---

## What I built

A desktop application that lets a user:

- choose a **main (carrier) image**
- choose a **hidden image** (same dimensions)
- **embed** the hidden image into the carrier image
- **extract** the hidden image back from an embedded image

![Application Interface](/assets/img/demo_ste_main.png){: .shadow w="900" }

---

## Why steganography (and how it differs from encryption)

Encryption makes data unreadable but obvious (“this is protected data”).  
Steganography aims to make data **hard to notice at all** by hiding it inside normal-looking files (images, audio, etc.).

This project focuses on the classic LSB technique because it’s simple, fast, and demonstrates core ideas in information hiding.

---

## How it works (LSB embedding)

### Core idea

Each RGB pixel channel is 8 bits (0–255). Human vision is relatively insensitive to tiny changes in the **least significant bits**, so we can replace those bits with information from a hidden image.

### Encryption (embed hidden image into carrier)

1. Load carrier + hidden images and verify **same width/height**
2. Clear the last `num_bits` bits of each carrier channel
3. Take the top `num_bits` bits from the hidden image channel and store them in the carrier’s LSBs
4. Save the result (the “encrypted” image)

### Decryption (recover hidden image)

1. Load the embedded image
2. Extract the last `num_bits` bits from each channel
3. Shift them back into the high bits to reconstruct a visible version of the hidden image
4. Save the recovered output

---

## Why `num_bits` matters (quality vs stealth)

The parameter `num_bits` controls the tradeoff:

- **Higher `num_bits`**  
  Hidden image recovers with more detail  
  Carrier image distortion becomes more noticeable

- **Lower `num_bits`**  
  Carrier image stays visually cleaner  
  Hidden image looks more “blocky” on recovery

In this implementation, `num_bits=2` is a practical default.

---

## Risks, threats, and what this does _not_ protect against

This project demonstrates **LSB image steganography**, which is best viewed as “hiding in plain sight,” not strong encryption.

**Threats / risks**

- **Steganalysis:** If an attacker suspects steganography, statistical analysis can sometimes detect unusual LSB patterns.
- **Extraction if discovered:** LSB embedding is reversible; if someone knows the method (and parameters like `num_bits`), they can attempt to recover the payload.
- **Image transformations break data:** Resizing, filtering, or especially **JPEG compression** can destroy embedded bits and make recovery impossible.
- **No secrecy by default:** Without encrypting the hidden image first, recovery reveals the hidden content directly.

**What it’s good for**

- Demonstrations, learning, and low-stakes concealment where the main goal is to avoid casual detection.

**How to harden it**

- Encrypt the hidden image (AES) before embedding.
- Use a key-based pseudo-random embedding pattern instead of sequential pixels.
- Add integrity checks (hash/MAC) to detect corruption.

---

## Key implementation excerpts

### Embed logic (LSB write)

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

### Extract logic (LSB read)

```python
def decrypt(encrypted_img_path, decrypted_img_path, num_bits=2):
    encrypted_img = Image.open(encrypted_img_path)
    encrypted_pixels = list(encrypted_img.getdata())

    mask = (1 << num_bits) - 1
    decrypted_pixels = [
        ((p[0] & mask) << (8 - num_bits),
         (p[1] & mask) << (8 - num_bits),
         (p[2] & mask) << (8 - num_bits))
        for p in encrypted_pixels
    ]

    decrypted_img = Image.new("RGB", encrypted_img.size)
    decrypted_img.putdata(decrypted_pixels)
    decrypted_img.save(decrypted_img_path)

```

---

## Limitations and security notes

This is a learning-focused implementation of LSB image hiding:

- **Not robust to compression/resizing**
  JPEG compression or scaling can destroy hidden bits.
- **No cryptographic secrecy** by default
  If someone suspects LSB steganography, extraction is possible.

### Improvements I would add next

- Password-based encryption of the hidden payload (AES) _before_ embedding
- Randomized embedding (keyed pseudo-random pixel positions)
- Basic steganalysis resistance (reduce detectable bit patterns)
- Support different image sizes (auto-resize with padding/cropping)

---

## Demo

### Main image (carrier)

Photo by Miguel Á. Padriñán: [https://www.pexels.com/photo/defocused-image-of-lights-255379/](https://www.pexels.com/photo/defocused-image-of-lights-255379/)

![Main Image](/assets/img/main.jpg){: .shadow w="700" }

### Hidden image (payload)

Photo by Anton Atanasov: [https://www.pexels.com/photo/close-up-photo-of-bowkey-735812/](https://www.pexels.com/photo/close-up-photo-of-bowkey-735812/)

![Hidden Image](/assets/img/hidden.jpg){: .shadow w="700" }

### Encrypted result (visually similar to the carrier)

![Encrypted Image](/assets/img/encrypted.jpg){: .shadow w="700" }
