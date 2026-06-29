# [cite_start]Software Requirement Specification (SRS) [cite: 1]

[cite_start]**Project:** Semantic Search Engine for Enterprise Knowledge Bases (Week 2 Module) [cite: 2]

---

## [cite_start]1. Introduction [cite: 3]

### [cite_start]1.1 Purpose [cite: 4]
[cite_start]The purpose of this document is to specify the requirements for the semantic search module being developed as part of a six-week agentic AI project. [cite: 5] [cite_start]This system is designed to replace or augment traditional exact-match keyword queries with context-aware, meaning-based information retrieval for enterprise documentation (such as IT, HR, and operations policies). [cite: 6]

### [cite_start]1.2 Scope [cite: 7]
[cite_start]This document specifies requirements for the semantic search module only, developed as part of Week 2 of a six-week agentic AI project. [cite: 8] [cite_start]It does not cover other components of the broader system being built in subsequent weeks. [cite: 9] [cite_start]The module ingests a small set of unstructured text documents, generates continuous vector representations (embeddings) using a pre-trained transformer model, and indexes these vectors within an in-memory database. [cite: 10] [cite_start]End-users can query the module using natural language, and it returns the top-k most relevant documents, exhibiting resilience against typos. [cite: 11] [cite_start]This version targets lightweight deployment in local or cloud-based notebook environments (e.g., Google Colab). [cite: 12]

### [cite_start]1.3 Overview [cite: 13]
[cite_start]The system loads a fixed set of HR/IT-style text documents, converts them into 384-dimensional embeddings using a pre-trained Sentence Transformer model, and stores them in an in-memory ChromaDB collection. [cite: 14] [cite_start]Users submit natural language queries, which are embedded using the same model and matched against the stored vectors to return the top 3 most relevant documents even when the query contains typos. [cite: 15]

---

## [cite_start]2. General Description [cite: 16]

### [cite_start]2.1 Product Perspective [cite: 17]
[cite_start]The Semantic Search Engine is a standalone, backend data-retrieval module implemented in a Python notebook. [cite: 18] [cite_start]It bridges a small in-code text dataset with a local in-memory vector instance (ChromaDB) and an embedding engine (Sentence Transformers). [cite: 19] [cite_start]It is one module of a larger six-week agentic AI project; [cite: 20] [cite_start]later weeks will build on or integrate with this module. [cite: 21]

### [cite_start]2.2 User Classes and Characteristics [cite: 22]
* [cite_start]**System Developers / Data Engineers:** Technical users who initialise the database, load documents, and run queries. [cite: 23]
* [cite_start]**End-Users (Employees/Staff):** Non-technical users who would query the knowledge base to find answers to standard corporate questions using natural language. [cite: 24]

### [cite_start]2.3 Definitions, Acronyms, and Abbreviations [cite: 25]
* [cite_start]**SRS:** System Requirements Specification [cite: 26]
* [cite_start]**Vector Embedding:** A numerical representation of text in a high-dimensional vector space where distance corresponds to semantic similarity. [cite: 27]
* [cite_start]**ChromaDB:** An open-source vector database designed for AI applications and embeddings. [cite: 28]
* [cite_start]**Similarity/Distance Metric:** A mathematical measure of closeness between two vectors. [cite: 29] [cite_start]Cosine similarity (0.0 to 1.0) is the intended target metric; [cite: 29] [cite_start]the current implementation uses ChromaDB's default distance function (see Design Constraints, 6.4). [cite: 30]
* [cite_start]**SQL:** Structured Query Language; used here as a conceptual baseline contrast for exact-match search. [cite: 31]
* [cite_start]**Top-k:** The number of highest-ranked results returned by a search (default k=3 in this system). [cite: 32]

### [cite_start]2.4 Assumptions and Dependencies [cite: 33]
* [cite_start]The execution environment is assumed to have internet access for a one-time download of the `all-MiniLM-L6-v2` model from Hugging Face Hub. [cite: 34]
* [cite_start]The environment is assumed to already have `sentence-transformers` and `pandas` available, or these must be manually installed before running the notebook. [cite: 36]
* [cite_start]No specific library versions are pinned; behavior is validated against versions resolved at runtime in Google Colab. [cite: 37]

---

## [cite_start]3. Functional Requirements [cite: 38]

### [cite_start]3.1 Environment Setup and Dependency Injection [cite: 39]
* [cite_start]**FR-1.1:** The system must install the `chromadb` package via `pip` at runtime. [cite: 40] [cite_start]The `sentence-transformers` and `pandas` packages are required as imports and are assumed pre-available in the execution environment, rather than explicitly installed in this version. [cite: 41, 42]

### [cite_start]3.2 Data Ingestion & Embedding Generation [cite: 43]
* [cite_start]**FR-2.1:** The system must accept a set of short text documents paired with unique string identifiers. [cite: 44]
* [cite_start]**FR-2.2:** The system must utilize the pre-trained `all-MiniLM-L6-v2` transformer model to process incoming text data. [cite: 46]
* [cite_start]**FR-2.3:** The model must transform input text into fixed 384-dimensional numeric vectors representing semantic content. [cite: 47]

### [cite_start]3.3 Vector Storage Management [cite: 48]
* [cite_start]**FR-3.1:** The system must initialize an in-memory instance of ChromaDB. [cite: 50]
* [cite_start]**FR-3.2:** The system must support creating a named database collection to isolate document embeddings. [cite: 51]
* [cite_start]**FR-3.3:** The database must index and associate unique document IDs, plain text contents, and generated vector representations together. [cite: 52]

### [cite_start]3.4 Semantic Query Execution and Ranking [cite: 53]
* [cite_start]**FR-4.1:** The system must accept free-form natural language query strings from the user. [cite: 54]
* [cite_start]**FR-4.2:** The system must generate a corresponding 384-dimensional runtime vector for the user's query string using the identical embedding model. [cite: 58]
* [cite_start]**FR-4.3:** The system must execute vector similarity searches against the ChromaDB index using ChromaDB's internal distance computation. [cite: 59]
* [cite_start]**FR-4.4:** The system must yield a ranked list of the top-k matching documents (default k=3). [cite: 60] [cite_start]Similarity/distance scores are computed internally but are not currently surfaced in the user-facing output. [cite: 61]

### [cite_start]3.5 Typo Resilience [cite: 62]
* [cite_start]**FR-5.1:** The system must correctly identify semantic matching intent even when the user input contains misspellings (e.g., identifying "nwe laptoop" as semantically mapping to "laptop replacement"). [cite: 63]

### [cite_start]3.6 Client-Specific Requirements [cite: 64]

#### [cite_start]3.6.1 Data Requirements (The Document Corpus) [cite: 65]
[cite_start]The system ingests the following sample HR/IT knowledge base, each entry assigned a unique document ID: [cite: 66, 67]

| Doc ID | Content |
| :--- | :--- |
| doc1 | [cite_start]Employees can request a laptop replacement after 3 years of use. [cite: 68] |
| doc2 | [cite_start]To report an incident, fill the form on the HR portal within 24 hours. [cite: 69] |
| doc3 | [cite_start]Reimbursement requests must be submitted within 30 days of the expense. [cite: 69] |
| doc4 | [cite_start]Work from home is allowed up to 3 days a week with manager approval. [cite: 69] |
| doc5 | [cite_start]Medical leave can be taken for up to 15 days per year with a doctor certificate. [cite: 69] |
| doc6 | [cite_start]New employees get onboarding training in their first week. [cite: 69] |
| doc7 | [cite_start]IT support can be reached at support@company.com or extension 1234. [cite: 69] |
| doc8 | [cite_start]Annual performance reviews happen every December. [cite: 69] |

#### [cite_start]3.6.2 Natural Language Mapping [cite: 70]
* [cite_start]**Requirement:** If a user searches "how do I get a new laptop", the system must return `doc1` (laptop replacement) as the top result, recognizing the semantic link between "new laptop" and "laptop replacement." [cite: 71]

#### [cite_start]3.6.3 Typo and Fault Tolerance [cite: 72]
* [cite_start]**Requirement:** A query such as "how do I get a nwe laptoop" (containing the typos "nwe" and "laptoop") must still resolve to `doc1` as a top-ranked result, rather than returning no results as an exact-match system would. [cite: 73]

#### [cite_start]3.6.4 Ranked Search Restraints (k-Value) [cite: 74]
* [cite_start]**Requirement:** The engine must limit output to the top 3 most contextually relevant matches to avoid overwhelming the user. [cite: 76]
* [cite_start]**Requirement:** Each returned match must display its ranking order (Result 1, Result 2, Result 3). [cite: 77]

---

## [cite_start]4. Interface Requirements [cite: 78]

### [cite_start]4.1 User Interface [cite: 79]
[cite_start]The system has no graphical user interface in this version. [cite: 80] [cite_start]All interaction occurs through a Jupyter/Colab notebook: users edit and run code cells, enter query strings as Python string variables, and view results printed to standard output. [cite: 81, 82]

### [cite_start]4.2 Software Interfaces [cite: 83]
* [cite_start]The system interfaces with the Sentence Transformers library via the `SentenceTransformer.encode()` method, which accepts a list of strings and returns a list of 384-dimensional numeric vectors. [cite: 84, 85]
* [cite_start]The system interfaces with ChromaDB via the `chromadb.Client()` API: `create_collection()` to initialize storage, `collection.add()` to store documents/embeddings/IDs, and `collection.query()` to retrieve top-k matches given a query embedding. [cite: 86]
* [cite_start]Data is passed between these two libraries as Python lists and NumPy arrays converted to lists (`embeddings.tolist()`), with no external file or network interface involved at query time. [cite: 87]

### [cite_start]4.3 Communication Interfaces [cite: 88]
* [cite_start]A one-time network call to Hugging Face Hub is required to download the `all-MiniLM-L6-v2` model weights on first run. [cite: 89] [cite_start]No other network communication occurs during normal operation (data ingestion and querying are fully local/in-memory). [cite: 90]

---

## [cite_start]5. Performance Requirements [cite: 91]
* [cite_start]**NFR-1.1:** Embedding generation and search queries over the current dataset (8 entries) are expected to complete within sub-second thresholds. [cite: 92] [cite_start]This is a design target, no timing benchmark has been implemented or measured in the current notebook. [cite: 93]
* [cite_start]**NFR-1.2:** The one-time model download (`all-MiniLM-L6-v2`) is dependent on network speed and is excluded from the runtime performance target above. [cite: 94]

---

## [cite_start]6. Design Constraints [cite: 95]
* [cite_start]**6.1 Language Constraint:** The core pipeline is fully implemented in Python, in a Jupyter/Colab notebook. [cite: 96]
* [cite_start]**6.2 Hardware and Environment:** Designed to run in resource-constrained environments such as local Jupyter Notebooks or Google Colab instances. [cite: 97]
* [cite_start]**6.3 Memory Dependency:** The database instance operates in-memory for the runtime session; [cite: 99] [cite_start]state is tied to the notebook's runtime lifecycle and is not persisted between sessions. [cite: 100]
* [cite_start]**6.4 Distance Metric:** The implementation uses ChromaDB's default distance function rather than an explicitly configured cosine-similarity space. [cite: 101] [cite_start]Cosine similarity is the intended target metric; explicit configuration is a planned refinement for a future version. [cite: 102]
* [cite_start]**6.5 In-Memory Security Constraint:** For data privacy reasons, document text and embeddings are processed and stored entirely in-memory, with no external database or cloud storage provider involved. [cite: 103, 104] [cite_start]The embedding model itself is downloaded once from Hugging Face Hub; [cite: 104] [cite_start]this involves model weights only, not document or query text. [cite: 105]

---

## [cite_start]7. Non-Functional Attributes [cite: 106]
* [cite_start]**NFR-2.1 (Accuracy/Relevance):** Vector search results are expected to surface contextually relevant matches even where exact-match SQL-style querying would fail on irregular phrasing or typos. [cite: 108] [cite_start]This is a design target; no direct SQL comparison query has been implemented to empirically demonstrate this contrast. [cite: 109]
* [cite_start]**NFR-3.1 (Usability/Maintainability):** Document ranking displays use readable numerical sequences generated via Python's `enumerate()` function. [cite: 110]
* [cite_start]**NFR-4.1 (Known Limitations / Verification Status):** NFR-1.1 (latency) and NFR-2.1 (SQL comparison) are stated as design targets and have not been benchmarked or empirically tested in the current implementation. [cite: 111] FR-4.3's target metric (cosine similarity) is not yet explicitly configured. [cite_start]These are documented as known gaps for a future iteration. [cite: 112]

---

## [cite_start]8. Preliminary Schedule and Budget [cite: 113]

### [cite_start]8.1 Schedule [cite: 114]
[cite_start]This module corresponds to Week 2 of a six-week agentic AI project. [cite: 115] [cite_start]The semantic search engine (this SRS's scope) was completed and tested in Week 2; [cite: 116] [cite_start]remaining weeks (3-6) will extend the project with additional components beyond this module's scope, to be specified in future SRS revisions or addenda. [cite: 117]

### [cite_start]8.2 Budget [cite: 118]
[cite_start]No monetary budget applies this is an academic/training project using free-tier tools (Google Colab, open-source libraries: ChromaDB, Sentence Transformers, Pandas). [cite: 119] [cite_start]The only resource cost is compute time on a free Colab runtime and one-time bandwidth for model download. [cite: 120]

---

## [cite_start]9. Appendices [cite: 121]

### [cite_start]9.1 References [cite: 122]
* [cite_start]ChromaDB documentation [cite: 124]
* [cite_start]Sentence Transformers (`all-MiniLM-L6-v2`) model card, Hugging Face Hub [cite: 125]
* [cite_start]`Week2_dune.ipynb` [cite: 125] [cite_start](the implementation notebook this SRS describes [cite: 127])

### [cite_start]9.2 Acronym Glossary [cite: 126]
[cite_start]*(See Section 2.3, Definitions, Acronyms, and Abbreviations.)* [cite: 128]
