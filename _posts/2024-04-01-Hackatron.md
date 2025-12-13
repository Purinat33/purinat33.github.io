---
title: "Healthcare Chatbot Application for MUICT Hackatron 2024"
categories: [Artificial Intelligence and Machine Learning, Large Language Model]
toc: true
---

# MUICT Hackatron 2024 — Healthcare Chatbot

We built a hospital assistant chatbot for **Siriraj Hospital** to help users:

- find illness-specific professionals,
- ask general hospital/doctor questions,
- and support appointment-related inquiries.

![Award](/assets/img/hackaton.jpg){: .shadow w="800" }

---

## What I contributed

This project’s biggest challenge wasn’t “writing lots of code” — it was making the chatbot **reliable** in a healthcare setting.

My main contributions were:

- Designing the **system prompt** and response constraints (Thai responses, structured outputs, “use only provided data”)
- Helping shape the **RAG approach** to reduce hallucinations
- Defining how the assistant should behave with ambiguous or casual user requests (infer intent → retrieve → answer)

> Why this matters: In domain chatbots, prompt/spec design is part of engineering. It determines safety, accuracy, and user trust.

---

## Problem

Large Language Models can generate answers that sound confident but are **not grounded** in real hospital/doctor information. In a healthcare context, hallucination is a major risk.

---

## Solution: Retrieval-Augmented Generation (RAG)

We used **Retrieval-Augmented Generation (RAG)** to ground the model in hospital-provided doctor data.

**High-level flow**

1. Store doctor information as documents
2. Convert documents into **embeddings**
3. Retrieve the most relevant chunks for the user query
4. Feed retrieved context into the LLM with strict instructions:
   - respond in Thai
   - use only the provided data
   - format information clearly (doctor name, specialty, timetable, etc.)

---

## Why the system prompt mattered

The system prompt acted like a “policy layer” that controlled:

- language (Thai-only)
- allowed knowledge (only retrieved documents)
- response structure (easy-to-read doctor info)
- behavior on vague user questions (infer intent, then answer)

This is the part we iterated the most because it directly impacted:

- hallucination rate
- correctness of doctor recommendations
- usability for real users

---

## Core terminology (quick)

### Embedding

A numeric representation of text that allows similarity search (e.g., related phrases map closer together in vector space).

### Vector store

A database designed to store embeddings and perform fast similarity search.

### Retriever

The component that finds the most relevant chunks from the vector store based on the user query.

---

## Implementation notes (what we built)

- **Frontend/UI:** Streamlit chat interface
- **RAG framework:** LlamaIndex
- **LLM:** OpenAI model (low temperature for stability)
- **Data source:** hospital doctor information (CSV/MD documents loaded into the index)

---

## Example: system prompt strategy (excerpt)

Instead of “be a helpful assistant,” we used strict constraints like:

- “Use only the data provided”
- “Do not hallucinate”
- “Always include doctor name + expertise + timetable when relevant”
- “Infer the user’s intent from casual language”

This helped keep responses aligned with the retrieved context.

---

## What I’d improve next

If we extended this beyond a hackathon prototype:

- Add citation-style output (show which document chunk supported each claim)
- Add “unknown / not found” handling (explicitly say when data isn’t available)
- Add evaluation: test set of common patient questions + scoring for groundedness
- Add safety boundaries (medical advice disclaimer + escalate to human staff)

---

## References

1. What is an Embedding: https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/rag/rag-generate-embeddings
2. Vector Store and Retriever: https://aws.amazon.com/what-is/retrieval-augmented-generation/
3. Code Repository: https://github.com/Purinat33/hackathon
