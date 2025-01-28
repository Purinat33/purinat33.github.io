---
title: "Healthcare Chatbot Application for MUICT Hackatron 2024"
categories: [Artificial Intelligence and Machine Learning, Large Language Model]
---

# MUICT Hackaton 2024

The purpose of this hackaton event is to create an assistant chatbot specialized in finding illness-specific professionals, making appointments, and general questions related to Siriraj Hospital.

![Award](/assets/img/hackaton.jpg)

## Problem and Solution

Knowing the tendency of Large Language Models to make up imaginary information, we used a technique called "Retrieval-Augmented Generation" to give our LLM extra information and references in the form of **context**.

By giving the LLM relevant information and instructing it to only use those information to answer, we can reduce the amount of hallucination occurred.

## Terminologies

### Embedding

Embedding is the a numerical representation of objects such as text. This is how we are able to retrieve relevant information before prompting, since closely related information would have similar embeddings (e.g. `cat` and `mammal`)

### Vector Store

Vector Store is a type of database that stores the embeddings of the data.

### Retriever

It is responsible for retreiving the relevant information from the Vector Store

### Embedding Model

Responsible for converting data into embeddings before storage.

## References:

1. [What is an Embedding](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/rag/rag-generate-embeddings)
2. [Vector Store and Retriever](https://aws.amazon.com/what-is/retrieval-augmented-generation/?utm_source=chatgpt.com)
