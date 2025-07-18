# **Mini LLM-Powered Question-Answering System Using RAG**

## **1\. Objective**

This project implements a simple, functional prototype of a document-based Question-Answering (QA) system using Retrieval-Augmented Generation (RAG) powered by an open-source Large Language Model (LLM). This system is designed to demonstrate an end-to-end RAG pipeline, from document ingestion to answer generation based on retrieved context.

## **2\. Assignment Scope & Implemented Components**

The following required components from the assignment have been implemented:

### **2.1. Document Ingestion and Chunking**

* **Loading:** The system features dynamic document upload capabilities via the Streamlit web interface. Users can upload text (.txt) or PDF (.pdf) files. The content is extracted and read into a string format.  
* **Chunking Strategy:**  
  * **Method:** langchain.text\_splitter.RecursiveCharacterTextSplitter is used to split the loaded document into manageable chunks.  
  * **Parameters:**  
    * chunk\_size: \[400\] characters. This size was chosen to ensure that each chunk contains sufficient context while remaining small enough for efficient embedding and retrieval.  
    * chunk\_overlap: \[40\] characters. Overlap is included to help maintain con
textual continuity between adjacent chunks, reducing the chance of losing critical information at chunk boundaries.  
  * **Justification:** RecursiveCharacterTextSplitter is robust and tries different separators (\\n\\n, \\n, , \`\`) to create semantically coherent chunks, which is beneficial for diverse document types.

### **2.2. Embedding and Vector Store**

* **Embedding Model:** all-MiniLM-L6-v2 from Sentence Transformers is used to generate dense vector embeddings for each document chunk. This model was chosen for its balance of performance, small size, and efficiency, making it suitable for a time-boxed challenge on a Colab free-tier GPU.  
* **Vector Store:** FAISS (Facebook AI Similarity Search) is employed as the vector store. FAISS was selected due to its high performance for similarity search on CPU (and GPU, though faiss-cpu is used for broader compatibility) and its seamless integration with LangChain.  
* **Retrieval:** The system is configured to retrieve the top-k (k=3) most similar chunks based on the semantic similarity between the user's query embedding and the stored chunk embeddings.

### **2.3. Query Interface (Streamlit Web Application)**

* **User Interface:** A simple Streamlit web interface has been implemented to provide a user-friendly way to interact with the QA system. This fulfills the optional bonus requirement for a UI.  
* **Dynamic Document Uploads:** The Streamlit interface includes a file uploader (st.file\_uploader) allowing users to dynamically upload their documents (.txt or .pdf).  
* **Query Input:** Users can type their questions into a text area within the web application.  
* **Context Retrieval:** Relevant context chunks are retrieved using semantic search from the FAISS vector store based on the user's query.

### **2.4. LLM Integration**

* **LLM Used:** TinyLlama/TinyLlama-1.1B-Chat-v1.0 is used as the open-source LLM.  
* **Reasoning for LLM Choice:** This model was specifically chosen for its extremely small size (1.1B parameters) which ensures it can be loaded and run efficiently on a Google Colab free-tier GPU (T4). While larger models might offer better answer quality, TinyLlama provides a functional demonstration of the RAG pipeline within the given resource constraints.  
* **Integration Method:** transformers.pipeline is used to wrap the TinyLlama model for text generation, and then langchain.llms.HuggingFacePipeline integrates this into the LangChain RAG chain. This approach provides a flexible and standard way to connect Hugging Face models with LangChain's RAG capabilities.  
* **Answer Generation:** The retrieved context chunks are "stuffed" into the LLM's prompt along with the user's query, enabling the LLM to generate a contextually relevant answer.

### **2.5. Output**

* The generated answer to the query is displayed directly within the Streamlit web interface.  
* The source document chunks that were used as context by the LLM are also displayed in the UI, providing transparency into the retrieval process.  
* A sample input and output for the required test question are provided below.

## **3\. Deliverables**

* **Code:** The complete code is provided in a Google Colab Notebook (Mini\_LLM\_Powered\_Question\_Answering\_System\_Using\_RAG.ipynb). This notebook generates an app.py file for the Streamlit application.  
* **README:** This document serves as the explanatory note.  
* **Sample Output:** (See Section 7 below)

## **4\. Tools and Models Used**

* **Python Libraries:**  
  * transformers (for LLM and tokenizer)  
  * sentence-transformers (for embedding model)  
  * faiss-cpu (for vector store)  
  * langchain (for RAG pipeline orchestration)  
  * streamlit (for web interface)  
  * pyngrok (to expose Streamlit app from Colab)  
  * PyPDF2 (for PDF document ingestion)  
* **Embedding Model:** all-MiniLM-L6-v2  
* **LLM:** TinyLlama/TinyLlama-1.1B-Chat-v1.0  
* **Development Environment:** Google Colab

### **4.1. Use of AI-Powered Development Tools**

This project, including both the code and this documentation, leveraged AI-powered development tools to enhance efficiency and accelerate the development process. These tools were utilized for:

* Assisting in structuring the Google Colab notebook and overall project layout.  
* Generating foundational code snippets for core RAG components ( vector store interaction) 
* Providing recommendations for suitable open-source LLMs compatible with resource constraints.  
* Supporting the refinement and debugging of various code segments.

## **5\. Setup and Running the Application**

1. **Open in Google Colab:** Open the provided Mini\_LLM\_Powered\_Question\_Answering\_System\_Using\_RAG.ipynb notebook in Google Colab.  
2. **Set Runtime:** Go to Runtime \-\> Change runtime type and select GPU as the hardware accelerator.  
3. **Run Cells:** Execute all cells sequentially.  
4. **Ngrok Token:** In the final code cell, replace "YOUR\_NGROK\_AUTH\_TOKEN" with your personal ngrok authentication token (obtainable from [https://dashboard.ngrok.com/get-started/your-authtoken](https://dashboard.ngrok.com/get-started/your-authtoken)).  
5. **Access Streamlit App:** After the last cell runs, a public ngrok URL will be displayed in the output. Click this URL to open the Streamlit web application in your browser.  
6. **Interact:** In the Streamlit app, use the file uploader to provide your document and then enter questions in the text area to get answers.

## **6\. Design Decisions and Assumptions**

* **Dynamic Document Input:** The system now fully supports dynamic document uploads directly within the Streamlit UI, allowing users to provide their own text-based (.txt) or PDF (.pdf) documents.  
* **CPU-friendly FAISS:** faiss-cpu was chosen over faiss-gpu to ensure broader compatibility, although a GPU runtime is still highly recommended for LLM inference.  
* **chain\_type="stuff":** This simple chain type was chosen for the MVP as it's effective for concatenating context and query into a single prompt. For more complex scenarios, other chain types (e.g., map\_reduce, refine) could be explored.  
* **Caching:** Streamlit's @st.cache\_resource and @st.cache\_data decorators are used to cache loaded models and processed document data, significantly improving performance by preventing redundant computations on app reruns.  
* **No Advanced Reranking:** Due to time constraints, advanced relevance reranking logic (e.g., Maximal Marginal Relevance) was not implemented. The top-k retrieval relies solely on embedding similarity.  
* **No Persistent Caching:** Caching is in-memory for the current session; no persistent caching for repeat queries across sessions is implemented.

## **7\. Limitations**

* **Time Constraints (4-hour limit):**  
  * While a Streamlit UI is implemented, it is a simple prototype. Further UI/UX refinements, advanced error handling, and more robust input validation would be needed for a production-ready application.  
  * Advanced document parsing (e.g., handling complex tables, images within PDFs, OCR) is not fully implemented; it relies on PyPDF2's text extraction, which can be imperfect for complex layouts.  
  * No persistent caching mechanism for repeat queries across different user sessions or restarts.  
  * No advanced relevance reranking.  
* **Resource Constraints:**  
  * The choice of LLM (TinyLlama) was dictated by the ability to run efficiently on a free Google Colab GPU (T4). Larger, more powerful LLMs were not feasible within these constraints.  
* **Answer Quality:** The quality of answers is directly dependent on the chosen LLM and the relevance of the retrieved chunks. TinyLlama is a small model, and while functional, its generative capabilities are limited compared to larger models.

## **8\. Sample Output for the Provided Query**

<img width="1491" height="683" alt="Screenshot 2025-07-18 131033" src="https://github.com/user-attachments/assets/76d670f8-7946-4fc0-a5bb-d5c3c2c3f615" />
<img width="1455" height="618" alt="Screenshot 2025-07-18 131207" src="https://github.com/user-attachments/assets/12dcc6b2-d021-4a09-bfa2-68edb8021141" />

<img width="1919" height="963" alt="image" src="https://github.com/user-attachments/assets/01d50794-ce85-4e6f-91ea-b6d15d09ae12" />
<img width="1915" height="964" alt="image" src="https://github.com/user-attachments/assets/961a55bd-6037-4e88-85e1-fed74542ce90" />

