# DATA6950_Secure_RAG_Framework

## Overview
This framework serves as a security-hardened implementation of Retrieval-Augmented Generation (RAG). While standard RAG systems significantly reduce LLM hallucinations by grounding responses in external data, they introduce a new attack surface: **Data Provenance Vulnerabilities**. If a retriever pulls in a malicious or "poisoned" document, the LLM can be coerced into ignoring system instructions, leaking data, or providing false information.

This project provides a comprehensive simulation environment to benchmark a standard RAG pipeline against two specific adversarial threats and validate a three-tier "Defense-in-Depth" architecture designed to maintain system integrity.

### Adversarial Attacks
To test the limits of the framework, two primary attack vectors are implemented:

* **Document Injection (Context Hijacking):** Simulation of a "noisy" or compromised corpus by injecting documents containing hidden system commands (e.g., "[SYSTEM OVERRIDE] Ignore previous data..."). The objective is to determine if the LLM adopts the malicious document's persona over the original programming.
* **Retrieval Poisoning (Vector Targeted Attack):** A sophisticated attack involving the creation of a document with a vector embedding nearly identical to a specific target query. This ensures that when a specific question is asked, the malicious document is mathematically guaranteed to be the Rank-1 result, bypassing traditional similarity thresholds.

### Security Defenses
These threats are countered using a "Sandwich" defense strategy that validates data at every stage of the RAG lifecycle:

* **Pre-Retrieval (Query Sanitization):** A specialized Query Re-writer acts as a gateway. It analyzes intent and strips away adversarial "jailbreak" syntax before the query reaches the database.
* **Mid-Retrieval (Context Auditing):** Every document retrieved by the FAISS index undergoes a secondary inference pass via an AI Auditor. This layer determines if the document contains passive information or active instructions designed to hijack the model.
* **Post-Generation (Output Integrity Check):** The final defense layer verifies the generated answer against the original clean query and the verified context. If the response deviates from the allowed context or follows a detected injection, a Security Alert is triggered and the output is blocked.


## Key Features

### Security Layers (The Defense-in-Depth Pipeline)
1.  **Query Re-writing Defense:** Employs a security-tuned prompt to strip adversarial instructions (e.g., "Ignore previous instructions") from user input before retrieval.
2.  **Context Auditor:** A dedicated LLM-based filter that evaluates retrieved documents for "Indirect Prompt Injection" before the generation stage.
3.  **Output Verification:** A final validation step checking the generated response against the context to ensure integrity and prevent "jailbreak" leaks.

### Adversarial Simulation Suite
* **Document Injection:** Insertion of malicious documents into the corpus to manipulate model output.
* **Retrieval Poisoning:** Demonstration of how "vector-identical" documents guarantee a malicious Rank-1 result.



## Technical Stack
* **LLM:** Meta Llama 3.2 (3B-Instruct)
* **Vector DB:** FAISS (FlatIP - Inner Product for Cosine Similarity)
* **Embedding Model:** `all-MiniLM-L6-v2`
* **Reranker:** `cross-encoder/ms-marco-MiniLM-L-6-v2`
* **Quantization:** 4-bit (via BitsAndBytes)
* **Dataset:** RealTimeQA (Sorted)


## Installation & Setup

### Prerequisites
* A **Hugging Face** account with granted access to Llama 3.2 weights.
* A Google Colab environment with a **T4 GPU** or higher.

### Core Dependencies
```bash
pip install torch transformers sentence-transformers faiss-cpu datasets accelerate bitsandbytes huggingface_hub
```

### Data Configuration
Ensure the `realtimeqa_sorted.json` dataset is located in Google Drive at:
`/drive/MyDrive/Colab Notebooks/Capstone II /`


## Pipeline Execution

The framework operates in three distinct modes:

### 1. Baseline RAG
Standard flow: `Retrieval` → `Reranking` → `Generation`. High utility, but vulnerable to poisoning.

### 2. Secure RAG
Implementation of the **Query Sanitizer** and **Context Auditor**. Documents flagged as `UNSAFE` are filtered out.

### 3. Ultimate Secure RAG
The most robust mode, adding **Output Verification** to ensure the final answer is not hijacked by latent instructions in the data.


## Evaluation Metrics
Performance is evaluated across three critical dimensions:
* **Recall@k:** Measures if the ground-truth answer exists within the retrieved candidates.
* **Semantic Similarity:** Uses Cosine Similarity to compare generated answers against gold-standard labels.
* **Robustness Score:** A custom metric calculating the success rate in rejecting or ignoring adversarial injections.


## Project Structure
* **STEP 1-3:** Environment setup, Data loading, and FAISS Indexing.
* **STEP 4-5:** Model initialization (Llama 3.2 & Cross-Encoders).
* **STEP 9:** Adversarial Attack implementations.
* **STEP 10-11:** Multi-layer Defense implementations.
* **Evaluation Suite:** Automated testing of Baseline vs. Protected models.
