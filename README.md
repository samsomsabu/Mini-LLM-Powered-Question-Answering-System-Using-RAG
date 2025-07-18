# Mini LLM-Powered Question-Answering System Using RAG

## 1. Objective

This project implements a simple, functional prototype of a document-based Question-Answering (QA) system using Retrieval-Augmented Generation (RAG) powered by an open-source Large Language Model (LLM). It demonstrates an end-to-end RAG pipeline, from document ingestion to answer generation based on retrieved context.

---

## 2. Assignment Scope & Implemented Components

### 2.1 Document Ingestion and Chunking

* **Loading:** Supports dynamic upload of text documents (PDF support included using PyPDF2).
* **Chunking Strategy:**

  * Method: `langchain.text_splitter.RecursiveCharacterTextSplitter`
  * Parameters:

    * `chunk_size`: 400 characters
    * `chunk_overlap`: 40 characters
  * Justification: Ensures semantically coherent chunks while preserving context between segments.

### 2.2 Embedding and Vector Store

* **Embedding Model:** `all-MiniLM-L6-v2` from Sentence Transformers

  * Chosen for its balance of accuracy and speed on Colab's free-tier GPU.
* **Vector Store:** FAISS (CPU version)

  * Enables fast similarity search based on query and chunk embeddings.
* **Retrieval:** Top-k (k=3) semantic search retrieves most relevant chunks.

### 2.3 Query Interface

* Accepts text query in Colab via an input prompt.
* Retrieves top-3 semantically relevant chunks from the FAISS index.

### 2.4 LLM Integration

* **LLM Used:** `TinyLlama/TinyLlama-1.1B-Chat-v1.0`
* **Why TinyLlama?**

  * Small (1.1B parameters), efficient, runs well on Colab T4 GPU
* **Integration Method:**

  * HuggingFace `transformers.pipeline` for inference
  * `langchain.llms.HuggingFacePipeline` for RAG chaining
* **Answer Generation:**

  * Retrieved context is concatenated with the query ("stuffed") into the LLM prompt.

### 2.5 Output

* **Generated Answer:** Displayed below the query.
* **Source Chunks:** The retrieved document segments shown for transparency.

---

## 3. Deliverables

* **Code:** Provided in `Mini_LLM_Powered_Question_Answering_System_Using_RAG.ipynb`
* **README:** This document
* **Sample Output:** See Section 7 below

---

## 4. Tools and Models Used

### 4.1 Python Libraries

* `transformers`, `sentence-transformers`, `faiss-cpu`, `langchain`
* `ipywidgets` (for file upload)
* `PyPDF2` (for PDF ingestion)

### 4.2 Models

* **Embedding:** `all-MiniLM-L6-v2`
* **LLM:** `TinyLlama/TinyLlama-1.1B-Chat-v1.0`

### 4.3 Development Environment

* Google Colab (Free-tier with T4 GPU)

## 4.1. Use of AI-Powered Development Tools

This project, including both the code and this documentation, leveraged AI-powered development tools (e.g., ChatGPT by OpenAI) to enhance efficiency and accelerate the development process. These tools were utilized for:

* Generating foundational code snippets for core RAG components (embedding, vector store interaction).
* Providing recommendations for suitable open-source LLMs compatible with resource constraints.
* Supporting the refinement and debugging of various code segments.


---

## 5. Design Decisions and Assumptions

* **Single Document Input:** Only one document is processed at a time.
* **FAISS-CPU:** Ensures wide compatibility, even without GPU acceleration.
* **Chain Type:** `stuff` used for prompt construction — simple and effective.
* **No Advanced Reranking:** Embedding similarity is used for relevance; no MMR.
* **No Caching:** Focus on core RAG logic; caching left out for simplicity.

---

## 6. Limitations

* **Time Constraint (\~4 hours):**

  * No Gradio/Streamlit interface
  * No sophisticated document parsing (e.g. for tables/images)
  * Basic error handling only
* **Resource Constraint (Colab):**

  * LLM size limited to what fits in free-tier GPU memory
* **Document Assumption:** Works best with plain text or text-based PDFs
* **Answer Quality:** Dependent on chunk quality + LLM capability (TinyLlama is limited)

---

## 7. Sample Output for Required Query

### Test Question:

"Give me the correct coded classification for the following diagnosis: 'Recurrent depressive disorder, currently in remission'"

### Generated Answer (Example):

**"The code for this diagnosis would be F33.4"**

### Source Documents (Retrieved Context):

**Chunk 1:**

```
currently depressed (F31.3, F31.4 or F31.5), a recurrent depressive disorder (F33. -) or a depressive episode (F32. -)...
```

**Chunk 2:**

```
F06.30 Organic manic disorder
F06.31 Organic bipolar affective disorder
F06.32 Organic depressive disorder
F06.33 Organic mixed affective disorder
```

**Chunk 3:**

```
recurrent depressive disorder (F33.-). These grades of severity are specified to cover a wide range of clinical states...
```

---
<img width="1491" height="683" alt="image" src="https://github.com/user-attachments/assets/b1e5b8bc-a316-43cd-9e12-141d0f0d5937" />
<img width="1455" height="618" alt="image" src="https://github.com/user-attachments/assets/4b5c08b4-35d3-4bd1-be9b-6a7d9ab11f60" />

## 8. Conclusion

This notebook demonstrates a working RAG pipeline on Google Colab using open-source tools. While simplified for resource constraints, it effectively illustrates key concepts in semantic retrieval and LLM-based question answering.
