# RAG from Scratch: A Conceptual Walkthrough

This repository contains a raw, dependency-light implementation of a Retrieval-Augmented Generation (RAG) pipeline.

The goal of this project was not to build a production-ready application, but to manually implement every step of the RAG cycle—extraction, chunking, embedding, and retrieval—to understand the underlying dynamics before moving to abstraction frameworks like LangChain.

**Note:** This is a "raw execution" designed for educational purposes. The chunking strategies and storage methods used here are intentionally basic to highlight where and why sophisticated tools are necessary.

---

## The Pipeline

This notebook (`RAG_Scratch.ipynb`) walks through the following manual steps:

### 1. Data Extraction
- Loading a PDF (Modern C book) using PyPDF2.
- Performing basic text cleaning (lowercasing, tab removal).

### 2. Naive Chunking
- Splitting text into fixed 300-character chunks.
- This approach often cuts words in half (for example, "pointer" becomes "poin" + "ter"), which reduces embedding quality.

### 3. Embeddings
- Generating vector representations using SentenceTransformer (`all-MiniLM-L6-v2`).

### 4. Vector Store
- Storing and indexing vectors using FAISS (CPU version) with L2 (Euclidean) distance.

### 5. Retrieval and Generation
- Querying the FAISS index for relevant chunks.
- Passing the retrieved context to Google Gemini (`gemini-2.5-flash`) to answer questions.

---

## Key Learnings

The primary takeaway from this experiment was understanding the bottlenecks of RAG:

### Chunking Matters
Hard-slicing text destroys semantic meaning. Intelligent chunking that respects sentence and paragraph boundaries is crucial.

### Context Quality
In the notebook, the LLM rated the retrieved context a 4/10 for the query "What are Pointers in C".  
The retriever found related keywords but failed to locate the actual definition because of the naive chunking strategy.

### Storage
Python dictionaries and in-memory FAISS indices do not scale. Production systems require persistent storage solutions such as PostgreSQL with pgvector.

---

## Dependencies

- transformers  
- PyPDF2  
- faiss-cpu  
- google-generativeai  
- sentence-transformers  

---

## Next Steps

With the core concepts validated and the limitations identified, the next stage of development will focus on:

- Migrating to LangChain for robust orchestration  
- Implementing recursive character text splitters to resolve word-cutting issues  
- Moving vector storage to PostgreSQL  

---

## How to Run

1. Upload a PDF named `modernC.pdf` (or adjust the filename in the code).  
2. Add your Google API Key to Colab secrets as `GOOGLE_API_KEY`.  
3. Run the cells sequentially to observe the pipeline in action.
