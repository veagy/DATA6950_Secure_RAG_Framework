# DATA6950_Secure_RAG_Framework

## Overview
This project implements a high-security **Retrieval-Augmented Generation (RAG)** pipeline designed to withstand modern LLM vulnerabilities. Beyond standard retrieval, this framework features a "Defense-in-Depth" architecture that simulates and mitigates **Document Injection** and **Retrieval Poisoning** attacks.

The system utilizes **Llama 3.2 (3B Instruct)** for generation, **FAISS** for vector indexing, and **Cross-Encoders** for high-precision reranking, all optimized with 4-bit quantization for performance on consumer-grade GPUs (T4).

---

## Key Features

###  Security Layers (The Defense-in-Depth Pipeline)
1.  **Query Re-writing Defense:** Uses a security-tuned prompt to strip adversarial instructions (e.g., "Ignore previous instructions") from user input before retrieval.
2.  **Context Auditor:** A dedicated LLM-based filter that evaluates retrieved documents for "Indirect Prompt Injection" before they reach the generation stage.
3.  **Output Verification:** A final validation step that checks the generated response against the context to ensure integrity and prevent "jailbreak" leaks.

###  Adversarial Simulation Suite
* **Document Injection:** Simulates the insertion of malicious documents into the corpus to manipulate model output.
* **Retrieval Poisoning:** Demonstrates how "vector-identical" documents can be used to guarantee a malicious Rank-1 result.

---

## Technical Stack
* **LLM:** Meta Llama 3.2 (3B-Instruct)
* **Vector DB:** FAISS (FlatIP - Inner Product for Cosine Similarity)
* **Embedding Model:** `all-MiniLM-L6-v2`
* **Reranker:** `cross-encoder/ms-marco-MiniLM-L-6-v2`
* **Quantization:** 4-bit (via BitsAndBytes)
* **Dataset:** RealTimeQA (Sorted)

---

## Installation & Setup

### Prerequisites
* A **Hugging Face** account and access to the Llama 3.2 weights.
* A Google Colab environment with a **T4 GPU** or higher.

### Core Dependencies
```bash
pip install torch transformers sentence-transformers faiss-cpu datasets accelerate bitsandbytes huggingface_hub
```

### Data Configuration
Ensure your `realtimeqa_sorted.json` dataset is located in your Google Drive at:
`/drive/MyDrive/Colab Notebooks/Capstone II /`

---

## Pipeline Execution

The framework operates in three distinct modes:

### 1. Baseline RAG
Standard flow: `Retrieval` → `Reranking` → `Generation`. High utility, but vulnerable to poisoning.

### 2. Secure RAG
Implements the **Query Sanitizer** and **Context Auditor**. It filters out any document flagged as `UNSAFE`.

### 3. Ultimate Secure RAG
The most robust mode, adding **Output Verification** to ensure the final answer has not been hijacked by latent instructions in the data.

---

## Evaluation Metrics
The project evaluates performance across three critical dimensions:
* **Recall@k:** Measures if the ground-truth answer exists within the retrieved candidates.
* **Semantic Similarity:** Uses Cosine Similarity to compare generated answers against gold-standard labels.
* **Robustness Score:** A custom metric calculating the system's success rate in rejecting or ignoring adversarial injections.

---

## Project Structure
* `STEP 1-3`: Environment setup, Data loading, and FAISS Indexing.
* `STEP 4-5`: Model initialization (Llama 3.2 & Cross-Encoders).
* `STEP 9`: Adversarial Attack implementations.
* `STEP 10-11`: Multi-layer Defense implementations.
* `Evaluation Suite`: Automated testing of Baseline vs. Protected models.

---
