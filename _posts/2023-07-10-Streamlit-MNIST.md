---
title: "Handwriting Recognition Webapp"
categories: [Artificial Intelligence and Machine Learning, Data Analytic]
---

# Creating a Handwriting Recognition Software using Deep Learning on a Web-Based Platform

[Link to Web Application](https://mnist-playground.streamlit.app/)

## Overview

The purpose of this web application is to allow for a convenient access to a simple handwritten digit classification application without the need to install complex libraries for a user to run on a given machine.

Additionally, an "Upload Your Digit" feature allows the user to upload an image of their own drawing outside of the pre-existing MNIST image files.

## Handwritten Digit Classification

The MNIST database for handwritten digit classification is considered by most to be an excellent introduction to the topic of deep learning and neural network.

## What is Streamlit

Streamlit is a Python library designed for fast and easy interactive web applications deployment, all for free.

## Classification Model

Using TensorFlow Library, a [Convolutional Neural Network](https://en.wikipedia.org/wiki/Convolutional_neural_network) model for digit image classification was built with the following structure:

![Model Structure](/assets/img/CNN_mnist.png)

## References:

1. [Streamlit Documentation](https://streamlit.io/)
2. [Code Repository](https://github.com/Purinat33/Streamlit-MNIST)
3. [Convolutional Layer](https://www.sciencedirect.com/topics/engineering/convolutional-layer)
4. [Convolutional Neural Network](https://en.wikipedia.org/wiki/Convolutional_neural_network)
