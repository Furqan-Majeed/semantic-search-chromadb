# Software Requirement Specification (SRS)

**Project:** Semantic Search Engine for Enterprise Knowledge Bases (Week 2 Module)

---

## 1. Introduction

### 1.1 Purpose
The purpose of this document is to specify the requirements for the semantic search module being developed as part of a six-week agentic AI project. This system is designed to replace or augment traditional exact-match keyword queries with context-aware, meaning-based information retrieval for enterprise documentation (such as IT, HR, and operations policies).

### 1.2 Scope
This document specifies requirements for the semantic search module only, developed as part of Week 2 of a six-week agentic AI project. It does not cover other components of the broader system being built in subsequent weeks. The module ingests a small set of unstructured text documents, generates continuous vector representations (embeddings) using a pre-trained transformer model, and indexes these vectors within an in-memory database. End-users can query the module using natural language, and it returns the top-k most relevant documents, exhibiting resilience against typos. This version targets lightweight deployment in local or cloud-based notebook environments (e.g., Google Colab).

### 1.3 Overview
The system loads a fixed set of HR/IT-style text documents, converts them into 384-dimensional embeddings using a pre-trained Sentence Transformer model, and stores them in an in-memory ChromaDB collection. Users submit natural language queries, which are embedded using the same model and matched against the stored vectors to return the top 3 most relevant documents even when the query contains typos.

---

## 2. General Description

### 2.1 Product Perspective
The Semantic Search Engine is a standalone, backend data-retrieval module implemented in a Python notebook. It bridges a small in-code text dataset with a local in-memory vector instance (ChromaDB) and an embedding engine (Sentence Transformers). It is one module of a larger six-week agentic AI project; later weeks will build on or integrate with this module.

### 2.2 User Classes and Characteristics
* **System Developers / Data Engineers:** Technical users who initialise the database, load documents, and run queries.
* **End-Users (Employees/Staff):** Non-technical users who would query the knowledge base to find answers to standard corporate questions using natural language.

### 2.3 Definitions, Acronyms, and Abbreviations
* **SRS:** System Requirements Specification
* **Vector Embedding:** A numerical representation of text in a high-dimensional vector space where distance corresponds to semantic similarity.
* **ChromaDB:** An open-source vector database designed for AI applications and embeddings.
* **Similarity/Distance Metric:** A mathematical measure of closeness between two vectors. Cosine similarity (0.0 to 1.0) is the intended target metric; the current implementation uses ChromaDB's default distance function (see Design Constraints, 6.4).
* **SQL:** Structured Query Language; used here as a conceptual baseline contrast for exact-match search.
* **Top-k:** The number of highest-ranked results returned by a search (default k=3 in this system).

### 2.4 Assumptions and Dependencies
* The execution environment is assumed to have internet access for a one-time download of the `all-MiniLM-L6-v2` model from Hugging Face Hub.
* The environment is assumed to already have `sentence-transformers` and `pandas` available, or these must be manually installed before running the notebook.
* No specific library versions are pinned; behavior is validated against versions resolved at runtime in Google Colab.

---

## 3. Functional Requirements

### 3.1 Environment Setup and Dependency Injection
* **FR-1.1:** The system must install the `chromadb` package via `pip` at runtime. The `sentence-transformers` and `pandas` packages are required as imports and are assumed pre-available in the execution environment, rather than explicitly installed in this version.

### 3.2 Data Ingestion & Embedding Generation
* **FR-2.1:** The system must accept a set of short text documents paired with unique string identifiers.
* **FR-2.2:** The system must utilize the pre-trained `all-MiniLM-L6-v2` transformer model to process incoming text data.
* **FR-2.3:** The model must transform input text into fixed 384-dimensional numeric vectors representing semantic content.

### 3.3 Vector Storage Management
* **FR-3.1:** The system must initialize an in-memory instance of ChromaDB.
* **FR-3.2:** The system must support creating a named database collection to isolate document embeddings.
* **FR-3.3:** The database must index and associate unique document IDs, plain text contents, and generated vector representations together.

### 3.4 Semantic Query Execution and Ranking
* **FR-4.1:** The system must accept free-form natural language query strings from the user.
* **FR-4.2:** The system must generate a corresponding 384-dimensional runtime vector for the user's query string using the identical embedding model.
* **FR-4.3:** The system must execute vector similarity searches against the ChromaDB index using ChromaDB's internal distance computation.
* **FR-4.4:** The system must yield a ranked list of the top-k matching documents (default k=3). Similarity/distance scores are computed internally but are not currently surfaced in the user-facing output.

### 3.5 Typo Resilience
* **FR-5.1:** The system must correctly identify semantic matching intent even when the user input contains misspellings (e.g., identifying "nwe laptoop" as semantically mapping to "laptop replacement").

### 3.6 Client-Specific Requirements

#### 3.6.1 Data Requirements (The Document Corpus)
The system ingests the following sample HR/IT knowledge base, each entry assigned a unique document ID:

| Doc ID | Content |
| :--- | :--- |
| doc1 | Employees can request a laptop replacement after 3 years of use. |
| doc2 | To report an incident, fill the form on the HR portal within 24 hours. |
| doc3 | Reimbursement requests must be submitted within 30 days of the expense. |
| doc4 | Work from home is allowed up to 3 days a week with manager approval. |
| doc5 | Medical leave can be taken for up to 15 days per year with a doctor certificate. |
| doc6 | New employees get onboarding training in their first week. |
| doc7 | IT support can be reached at support@company.com or extension 1234. |
| doc8 | Annual performance reviews happen every December. |

#### 3.6.2 Natural Language Mapping
* **Requirement:** If a user searches "how do I get a new laptop", the system must return `doc1` (laptop replacement) as the top result, recognizing the semantic link between "new laptop" and "laptop replacement."

#### 3.6.3 Typo and Fault Tolerance
* **Requirement:** A query such as "how do I get a nwe laptoop" (containing the typos "nwe" and "laptoop") must still resolve to `doc1` as a top-ranked result, rather than returning no results as an exact-match system would.

#### 3.6.4 Ranked Search Restraints (k-Value)
* **Requirement:** The engine must limit output to the top 3 most contextually relevant matches to avoid overwhelming the user.
* **Requirement:** Each returned match must display its ranking order (Result 1, Result 2, Result 3).

---

## 4. Interface Requirements

### 4.1 User Interface
The system has no graphical user interface in this version. All interaction occurs through a Jupyter/Colab notebook: users edit and run code cells, enter query strings as Python string variables, and view results printed to standard output.

### 4.2 Software Interfaces
* The system interfaces with the Sentence Transformers library via the `SentenceTransformer.encode()` method, which accepts a list of strings and returns a list of 384-dimensional numeric vectors.
* The system interfaces with ChromaDB via the `chromadb.Client()` API: `create_collection()` to initialize storage, `collection.add()` to store documents/embeddings/IDs, and `collection.query()` to retrieve top-k matches given a query embedding.
* Data is passed between these two libraries as Python lists and NumPy arrays converted to lists (`embeddings.tolist()`), with no external file or network interface involved at query time.

### 4.3 Communication Interfaces
* A one-time network call to Hugging Face Hub is required to download the `all-MiniLM-L6-v2` model weights on first run. No other network communication occurs during normal operation (data ingestion and querying are fully local/in-memory).

---

## 5. Performance Requirements
* **NFR-1.1:** Embedding generation and search queries over the current dataset (8 entries) are expected to complete within sub-second thresholds. This is a design target, no timing benchmark has been implemented or measured in the current notebook.
* **NFR-1.2:** The one-time model download (`all-MiniLM-L6-v2`) is dependent on network speed and is excluded from the runtime performance target above.

---

## 6. Design Constraints
* **6.1 Language Constraint:** The core pipeline is fully implemented in Python, in a Jupyter/Colab notebook.
* **6.2 Hardware and Environment:** Designed to run in resource-constrained environments such as local Jupyter Notebooks or Google Colab instances.
* **6.3 Memory Dependency:** The database instance operates in-memory for the runtime session; state is tied to the notebook's runtime lifecycle and is not persisted between sessions.
* **6.4 Distance Metric:** The implementation uses ChromaDB's default distance function rather than an explicitly configured cosine-similarity space. Cosine similarity is the intended target metric; explicit configuration is a planned refinement for a future version.
* **6.5 In-Memory Security Constraint:** For data privacy reasons, document text and embeddings are processed and stored entirely in-memory, with no external database or cloud storage provider involved. The embedding model itself is downloaded once from Hugging Face Hub; this involves model weights only, not document or query text.

---

## 7. Non-Functional Attributes
* **NFR-2.1 (Accuracy/Relevance):** Vector search results are expected to surface contextually relevant matches even where exact-match SQL-style querying would fail on irregular phrasing or typos. This is a design target; no direct SQL comparison query has been implemented to empirically demonstrate this contrast.
* **NFR-3.1 (Usability/Maintainability):** Document ranking displays use readable numerical sequences generated via Python's `enumerate()` function.
* **NFR-4.1 (Known Limitations / Verification Status):** NFR-1.1 (latency) and NFR-2.1 (SQL comparison) are stated as design targets and have not been benchmarked or empirically tested in the current implementation. FR-4.3's target metric (cosine similarity) is not yet explicitly configured. These are documented as known gaps for a future iteration.

---

## 8. Preliminary Schedule and Budget

### 8.1 Schedule
This module corresponds to Week 2 of a six-week agentic AI project. The semantic search engine (this SRS's scope) was completed and tested in Week 2; remaining weeks (3-6) will extend the project with additional components beyond this module's scope, to be specified in future SRS revisions or addenda.

### 8.2 Budget
No monetary budget applies this is an academic/training project using free-tier tools (Google Colab, open-source libraries: ChromaDB, Sentence Transformers, Pandas). The only resource cost is compute time on a free Colab runtime and one-time bandwidth for model download.

---

## 9. Appendices

### 9.1 References
* ChromaDB documentation
* Sentence Transformers (`all-MiniLM-L6-v2`) model card, Hugging Face Hub
* `Week2_dune.ipynb` (the implementation notebook this SRS describes)

### 9.2 Acronym Glossary
*(See Section 2.3, Definitions, Acronyms, and Abbreviations.)*
