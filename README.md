# semantic-search-chromadb

A practical tutorial on building a semantic search engine using ChromaDB, Sentence Transformers, and Python.

## Overview
This repository contains a Jupyter Notebook (`Week2_dune.ipynb`) that serves as a practical introduction to building a semantic search engine. It demonstrates how to use **ChromaDB** (a vector database) and Hugging Face's **Sentence Transformers** to perform meaning-based text retrieval. 

The project highlights the advantages of vector search over traditional SQL queries, particularly its ability to understand context, synonyms, and handle typos seamlessly.

## Key Features
* **Vector Embeddings:** Uses the pre-trained `all-MiniLM-L6-v2` model to convert textual data into 384-dimensional numeric vectors that capture semantic meaning.
* **In-Memory Vector Database:** Utilizes ChromaDB to store documents, their unique IDs, and their generated embeddings for rapid querying.
* **Semantic Search Implementation:** Performs cosine similarity-based queries to retrieve the most relevant documents based on search intent, rather than exact keyword matches.
* **Typo Resilience:** Contains a practical demonstration of how vector search successfully retrieves correct results even when the user query contains significant spelling errors (a scenario where exact-match SQL fails completely).

## Concepts Covered
* **Embeddings:** Converting human-readable text into mathematical spatial representations.
* **Similarity Scores (Cosine Similarity):** Understanding how the angles between vectors determine semantic closeness (e.g., 1.0 = identical meaning, 0.0 = completely opposite).
* **Vector Search vs. Traditional SQL:** A comparative look at exact character matching versus meaning-based evaluation.
* **Python Utilities:** A brief tutorial on using Python's `enumerate()` function for cleanly formatting ranked search results.

## Installation & Setup
To run this notebook locally or in cloud environments like Google Colab, install the required dependencies:

```bash
pip install chromadb sentence-transformers pandas


Quick Start
Clone the repository and open Week2_dune.ipynb in your Jupyter environment (or upload it to Google Colab).

Run the first cell to install the required libraries.

The notebook will step-by-step guide you through:

Defining a sample dataset (e.g., an IT/HR policy knowledge base).

Initializing the embedding model and generating text vectors.

Creating a ChromaDB collection and storing the data.

Querying the index with clean text and text containing intentional typos.

Example Output
Even when querying with intentional typos like "how do I get a nwe laptoop", the vector database understands the intent:

Query WITH typo: how do I get a nwe laptoop
Top 3 Results (even with typo!):
Result 1: IT support can be reached at support@company.com or extension 1234.
Result 2: Employees can request a laptop replacement after 3 years of use.
Result 3: New employees get onboarding training in their first week.
