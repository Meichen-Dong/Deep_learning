# RAG System for Exchange Program Question Answering

The goal of this project is to build a Retrieval-Augmented Generation (RAG) system that helps BSc students quickly understand exchange program requirements using official documents.

## Overview

This project implements a Retrieval-Augmented Generation (RAG) pipeline to assist BSc students in exploring exchange opportunities.

Instead of manually going through multiple PDF documents, users can ask questions in natural language and receive concise answers based on the official program materials.

The system retrieves relevant text from a ChromaDB vector database, builds a prompt, and generates answers using a Hugging Face LLM. A simple Gradio interface is provided for interaction.


## Repository Structure

```
Deep_learning/
├── database/               # 8 exchange program PDFs (knowledge base)
│   ├── AGH.pdf
│   ├── Cork.pdf
│   ├── ICESI.pdf
│   ├── ITBA.pdf
│   ├── Sungkyunkwan_business.pdf
│   ├── Sungkyunkwan_computer.pdf
│   ├── Taibei.pdf
│   └── Wroclaw.pdf
├── notebooks/
│   └── rag_project.ipynb   # Main notebook (full pipeline + evaluation)
├── .gitignore
└── RAG_(slide_pdf).pdf
└── RAG.pptx
└── README.md  
```


## Setup

### 1. Install dependencies

```bash
pip install transformers chromadb sentence-transformers huggingface-hub \
            langchain_community langchain-text-splitters pypdf \
            gradio tqdm python-dotenv
```

### 2. Set the Hugging Face token

Create a file `notebooks/.env` with:

```
HF_TOKEN=your_token_here
```

Get a free token at [huggingface.co/settings/tokens](https://huggingface.co/settings/tokens).

### 3. Update the PDF folder path

In the notebook, cell **1. Data Preparation**, update the `folder` variable to point to your local `database/` directory:

```python
folder = r"path/to/your/database"
```

### 4. Run the notebook

Open `notebooks/rag_project.ipynb` and run all cells. The Gradio interface launches automatically at the end.

---

## Pipeline

| Step | Component |
|------|-----------|
| Document loading | LangChain `PyPDFLoader` |
| Text chunking | `RecursiveCharacterTextSplitter` (chunk=300, overlap=50) |
| Embedding | `all-MiniLM-L6-v2` (Sentence Transformers) |
| Vector store | ChromaDB `PersistentClient` |
| LLM | `HuggingFaceH4/zephyr-7b-beta` via HF Inference API |
| Interface | Gradio |

---

## Evaluation

The notebook includes the following experiments (Section 6):

- **6.1** RAG vs. No RAG — confirms retrieval is essential
- **6.2** Two embedding models — `all-MiniLM-L6-v2` vs `paraphrase-MiniLM-L3-v2`
- **6.3** Number of retrieved chunks — tested n = 1, 2, 4, 6
- **6.4** Response time — average ~2.3s end-to-end
- **6.5** Two LLMs — `zephyr-7b-beta` vs `Qwen2.5-72B-Instruct`
- **6.6** Prompt templates — Basic / Structured (XML tags) / Role-based
- **6.7** Accuracy — 75% on a manual evaluation set of 8 questions

---

## Example Questions

- *What are the language requirements for AGH University in Krakow?*
- *What is the minimum GPA for National Taipei University?*
- *Which universities require Spanish?*
- *When does the fall semester start at Wroclaw University?*
