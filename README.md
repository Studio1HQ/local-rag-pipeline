<div align="center">

<img src="https://raw.githubusercontent.com/Studio1HQ/Studio1HQ/main/assets/banners/local-rag-pipeline.webp" alt="Local RAG Pipeline with Bright Data: Scrape with Bright Data, then embed and answer fully locally" width="100%" />

<br />
<br />

# Local RAG Pipeline with Bright Data

### Scrape with Bright Data, then embed and answer fully locally

A three-script RAG pipeline: Bright Data Web Unlocker fetches pages as Markdown, ChromaDB stores sentence-transformer embeddings, and a local Ollama model answers questions with sources.

<br />

[![Built by Studio1](https://img.shields.io/badge/Built_by_Studio1-f97316?style=for-the-badge&logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAxNjAgMTYwIiBmaWxsPSJub25lIj48cGF0aCBmaWxsLXJ1bGU9ImV2ZW5vZGQiIGNsaXAtcnVsZT0iZXZlbm9kZCIgZD0iTTAgMzVDMCAxNi42ODA3IDE0LjA3NDQgMS42NDg0NCAzMiAwLjEyNjk1M1YxNDFINTEuMDUyM0M1Mi40MzA4IDEzNy4zNDQgNTQuNzQ0NiAxMzMuNjg5IDU3Ljk5MzggMTMwLjAzM0M2MS4xNDQ2IDEyNi41MDggNjQuNjg5MiAxMjMuNDQgNjguNjI3NyAxMjAuODI5QzcyLjU2NjIgMTE4LjM0OSA3Ni4zNTY5IDExNi43ODIgODAgMTE2LjEyOVY4Ni43NTQ0Qzc1LjQ3MDggODcuOTI5MiA3MS4zODQ2IDg5LjY5MTkgNjcuNzQxNSA5Mi4wNDE1QzY0IDk0LjUyMjUgNjAuNzUwOCA5Ny40NiA1Ny45OTM4IDEwMC44NTRDNTYuNTM0NSAxMDIuNzIgNTUuMjU0NSAxMDQuNjU5IDU0LjE1MzkgMTA2LjY3MVYwSDEyNUMxNDQuMzMgMCAxNjAgMTUuNjY5OSAxNjAgMzVWMTI1QzE2MCAxNDQuMzMgMTQ0LjMzIDE2MCAxMjUgMTYwSDEyNFYxOEgxMDQuOTQ4QzEwMy41NjkgMjEuNjgxNiAxMDEuMjU1IDI1LjM2MjggOTguMDA2MiAyOS4wNDQ0Qzk0Ljg1NTQgMzIuNTk0MiA5MS4zMTA4IDM1LjY4NDEgODcuMzcyMyAzOC4zMTRDODMuNDMzOCA0MC44MTIgNzkuNjQzMSA0Mi4zODk2IDc2IDQzLjA0NzRWNzIuNjMwNEM4MC41MjkyIDcxLjQ0NzMgODQuNjE1NCA2OS42NzI0IDg4LjI1ODUgNjcuMzA1N0M5MiA2NC44MDc2IDk1LjI0OTIgNjEuODQ5MSA5OC4wMDYyIDU4LjQzMDdDOTkuNDY1NSA1Ni41NTEzIDEwMC43NDUgNTQuNTk4NiAxMDEuODQ2IDUyLjU3MjNWMTYwSDM1QzE1LjY3IDE2MCAwIDE0NC4zMyAwIDEyNVYzNVoiIGZpbGw9IiNmZmZmZmYiLz48L3N2Zz4K)](https://studio1hq.com)
[![Built with Bright Data](https://img.shields.io/badge/Built_with_Bright_Data-141414?style=for-the-badge)](https://brightdata.com)

[Bright Data docs](https://docs.brightdata.com) &nbsp;·&nbsp; [Bright Data](https://brightdata.com) &nbsp;·&nbsp; [More examples](https://github.com/Studio1HQ) &nbsp;·&nbsp; [studio1hq.com](https://studio1hq.com)

</div>

<br />

## Overview

A minimal, local Retrieval-Augmented Generation pipeline in three scripts. It scrapes web pages, embeds them into a local vector store, and answers questions with a local LLM.

The Bright Data Web Unlocker API (`https://api.brightdata.com/request`) fetches each page as Markdown. Everything after the scrape runs on your machine: embeddings and the LLM never leave it.

```text
collect.py  ->  data/raw/pages.json  ->  ingest.py  ->  data/chroma  ->  rag.py  ->  answer
 (scrape)                                 (chunk +                       (retrieve +
                                           embed)                         generate)
```

## Features

- **Web Unlocker collection**: `collect.py` fetches target URLs as Markdown (default: three Wikipedia articles on RAG, vector databases and LLMs).
- **Local embeddings**: `ingest.py` chunks pages and embeds them with `all-MiniLM-L6-v2`.
- **Persistent index**: ChromaDB collection `web_knowledge` at `data/chroma`. Re-ingesting is idempotent because chunk IDs are stable.
- **Local answers**: `rag.py` retrieves the top matching chunks and asks `llama3.2:3b` through Ollama, answering only from that context.
- **Sources shown**: Prints the answer and the URLs it came from.
- **Ready to query**: The repo ships scraped data and a built index, so you can skip scraping.

## Tech stack

| Layer | Tool |
| --- | --- |
| Scraping | [Bright Data Web Unlocker](https://brightdata.com) API |
| Embeddings | [sentence-transformers](https://www.sbert.net) `all-MiniLM-L6-v2` |
| Vector store | [ChromaDB](https://www.trychroma.com) |
| LLM | [Ollama](https://ollama.com) `llama3.2:3b` |

## Getting started

### Prerequisites

- Python 3.9+
- [Ollama](https://ollama.com) running on `localhost:11434` with `llama3.2:3b` pulled
- A [Bright Data](https://brightdata.com) account with a Web Unlocker zone (only to re-scrape)

### Installation

```bash
git clone https://github.com/Studio1HQ/local-rag-pipeline.git
cd local-rag-pipeline
python -m venv venv
source venv/bin/activate
pip install requests chromadb sentence-transformers
ollama pull llama3.2:3b
ollama serve   # if not already running
```

### Environment variables

This project does not read environment variables. Set these constants in `collect.py` before scraping:

| Variable | Description |
| --- | --- |
| `API_KEY` | Your Bright Data API key (empty in the repo). |
| `ZONE` | Web Unlocker zone name. Default `web_unlocker1`. |

Avoid committing a real key.

### Run

```bash
# 1. Collect pages (optional, data is included)
python collect.py

# 2. Build the vector index
python ingest.py

# 3. Ask a question
python rag.py "What is retrieval-augmented generation?"
```

Example output:

```text
=== Answer ===
Retrieval-augmented generation (RAG) is a technique that ...

=== Sources ===
  - https://en.wikipedia.org/wiki/Retrieval-augmented_generation
```

## How it works

| Setting | Location | Default |
| --- | --- | --- |
| Target URLs | `collect.py` `TARGETS` | 3 Wikipedia pages |
| Chunk size and overlap | `ingest.py` `chunk_text()` | 200 words, 30 overlap |
| Embedding model | `ingest.py`, `rag.py` | `all-MiniLM-L6-v2` |
| Collection name | `ingest.py`, `rag.py` | `web_knowledge` |
| LLM model | `rag.py` `MODEL` | `llama3.2:3b` |
| Retrieved chunks | `rag.py` `retrieve()` | 3 |
| Ollama endpoint | `rag.py` `OLLAMA_URL` | `http://localhost:11434` |

```text
.
├── collect.py   # scrape URLs to data/raw/pages.json
├── ingest.py    # chunk and embed to data/chroma
├── rag.py       # retrieve and generate with Ollama
└── data/
    ├── raw/
    └── chroma/
```

## Resources

- [Bright Data docs](https://docs.brightdata.com): Web Unlocker API reference.
- [Bright Data](https://brightdata.com): Account and zone setup.
- [Ollama](https://ollama.com): Local model runtime.

<br />

---

<div align="center">

<a href="https://studio1hq.com">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/Studio1HQ/website/main/brand/studio1-logo-on-dark.svg" />
    <img src="https://raw.githubusercontent.com/Studio1HQ/website/main/brand/studio1-logo-on-light.svg" alt="Studio1" width="160" />
  </picture>
</a>

Built by **[Studio1](https://studio1hq.com)**, the technical content and DevRel partner for devtool teams. Made with [Bright Data](https://brightdata.com).<br />
Browse more open-source examples at [github.com/Studio1HQ](https://github.com/Studio1HQ).

[![Website](https://img.shields.io/badge/studio1hq.com-0b0807?style=for-the-badge&logo=googlechrome&logoColor=white)](https://studio1hq.com)
[![Book a call](https://img.shields.io/badge/Book_a_strategy_call-f97316?style=for-the-badge&logo=googlecalendar&logoColor=white)](https://cal.com/studio1/collab)
[![X](https://img.shields.io/badge/%40Studio1HQ-141414?style=for-the-badge&logo=x&logoColor=white)](https://x.com/Studio1HQ)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0a66c2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/company/studio1hq/)

<sub><a href="https://studio1hq.com">studio1hq.com</a> · Technical content, docs, video and DevRel for devtools</sub>

</div>
