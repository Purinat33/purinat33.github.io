---
title: "Handwriting Recognition Webapp"
categories: [Artificial Intelligence and Machine Learning, Data Analytic]
toc: true
---

# Handwriting Recognition Webapp (MNIST) with a CNN + Streamlit

![Index](/assets/img/cnn_mnist_index.png)

A lightweight web app that classifies handwritten digits using a Convolutional Neural Network (CNN). Users can either sample from MNIST or upload their own digit image—no local ML setup required.

- Live app: https://mnist-playground.streamlit.app/
- Code repo: https://github.com/Purinat33/Streamlit-MNIST

> Goal: make a deep-learning demo accessible from any browser while keeping the ML workflow (preprocess → predict → visualize) transparent.

---

## Project at a glance

| Item            | Details                                                                      |
| --------------- | ---------------------------------------------------------------------------- |
| Problem         | Classify handwritten digits (0–9) from images                                |
| Model           | TensorFlow CNN (image classification)                                        |
| Dataset         | MNIST handwritten digits                                                     |
| Web framework   | Streamlit (Python)                                                           |
| Key features    | MNIST sample prediction, image upload prediction, simple UI for fast testing |
| What this shows | End-to-end ML delivery: model + preprocessing + web deployment               |

---

## Why I built this

Many ML demos require installing Python, TensorFlow, and dependencies. This project packages the experience into a **web-based interface** so anyone can try digit recognition from a browser.

---

## What the user can do

1. **Predict a digit from MNIST**

   - Select an image from the dataset
   - View the predicted label and model confidence

2. **Upload Your Digit**
   - Upload a hand-drawn digit image
   - The app preprocesses it to match MNIST input format, then runs inference

![Play](/assets/img/cnn_play.png)

---

## How it works

### System flow

1. User provides an image (MNIST sample or upload)
2. App preprocesses the image to MNIST-like format (28×28, grayscale, normalized)
3. CNN predicts probabilities for digits 0–9
4. UI displays predicted digit

---

## Model

I trained a **Convolutional Neural Network** in TensorFlow for digit classification.

![Model Structure](/assets/img/CNN_mnist.png)

### Why a CNN?

CNNs are strong at learning spatial patterns in images (edges, curves, strokes), which fits handwritten digits well.

---

## Preprocessing (important for uploads)

Uploads often fail if preprocessing doesn’t match the training data. The app should ensure:

- Convert to **grayscale**
- Resize to **28×28**
- Normalize pixel values (e.g., 0–1)
- Ensure background/foreground matches MNIST style (often white digit on black background, depending on your implementation)

---

## Limitations

- MNIST is a clean benchmark dataset; real-world handwriting can be noisier.
- Uploaded images vary in contrast, thickness, and centering, which can reduce accuracy.

---

## Future improvements

- Add a **draw-on-canvas** input (in-browser) so users don’t need to upload files
- Show **probability bars** for all digits (0–9) to make predictions interpretable
- Improve robustness with:
  - data augmentation during training (rotation/shift/scale)
  - better upload preprocessing (auto-crop, auto-center, thresholding)
- Expand beyond MNIST (e.g., EMNIST letters) or multi-digit recognition

---

## References

1. Streamlit Documentation: https://streamlit.io/
2. Code Repository: https://github.com/Purinat33/Streamlit-MNIST
3. Convolutional Layer: https://www.sciencedirect.com/topics/engineering/convolutional-layer
4. Convolutional Neural Network: https://en.wikipedia.org/wiki/Convolutional_neural_network
