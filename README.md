# 🏛 Met Museum Multimodal RAG (OpenAI API)

> Semantic search across 470,000+ artworks from The Metropolitan Museum of Art using natural language — powered by **OpenAI GPT (vision)**, **OpenAI embeddings**, **ChromaDB**, and the **Met Open Access API**.

![Python](https://img.shields.io/badge/Python-3.10+-blue?style=flat-square&logo=python)
![Streamlit](https://img.shields.io/badge/Streamlit-1.35+-red?style=flat-square&logo=streamlit)
![ChromaDB](https://img.shields.io/badge/ChromaDB-0.5+-green?style=flat-square)
![OpenAI](https://img.shields.io/badge/OpenAI-GPT--4.1-black?style=flat-square)
![License](https://img.shields.io/badge/Data-CC0%20Met%20Open%20Access-lightgrey?style=flat-square)

---

## Demo

| Query | Top Result |
|---|---|
| *"melancholic women in Renaissance portraits"* | Portrait of a Young Woman — Rogier van der Weyden |
| *"Japanese woodblock prints with ocean waves"* | The Great Wave off Kanagawa — Hokusai |
| *"ancient Egyptian gold artifacts"* | Gold Funerary Mask, Dynasty 21 |
| *"Abstract Expressionist paintings with bold color"* | Number 31 — Jackson Pollock |

---

## Architecture

```
User Prompt
│
▼
┌─────────────────────────────────┐
│ 1. ChromaDB Retrieval │ OpenAI embeddings (text-embedding-3-large)
│ Semantic similarity search │ ~5k–20k artworks indexed locally
└──────────────┬──────────────────┘
│ top-N candidates
▼
┌─────────────────────────────────┐
│ 2. Met API Fetch │ live REST API · no key required
│ Metadata + image URLs │
└──────────────┬──────────────────┘
│
▼
┌─────────────────────────────────┐
│ 3. GPT Re-ranking │ multimodal (text + images)
│ Vision + reasoning │ returns score + explanation
└──────────────┬──────────────────┘
│
▼
┌─────────────────────────────────┐
│ 4. Streamlit UI │ image gallery + explanations
└─────────────────────────────────┘
```


---

## Why this is a real RAG system

| RAG Component | Role | Technology |
|---|---|---|
| **Retrieval** | Semantic search over artworks | ChromaDB + OpenAI embeddings |
| **Augmentation** | Live metadata + images | Met Museum API |
| **Generation** | Re-ranking + reasoning | OpenAI GPT (vision + text) |

Unlike keyword search, this understands prompts like:

> *"melancholic women in Renaissance portraits"*

and ranks results using **semantic + visual understanding**.

---

## Tech Stack

`Python` · `Streamlit` · `OpenAI API` · `GPT-4.1 / GPT-4o` · `OpenAI Embeddings (text-embedding-3-large)` · `ChromaDB` · `RAG` · `Met Museum API` · `HNSW indexing` · `semantic search` · `multimodal reasoning` · `Pillow` · `requests`

---

## Project Structure

```
met-museum-rag/
├── app.py                  # Streamlit frontend
├── build_index.py          # One-time index builder
├── requirements.txt
├── README.md
├── src/
│   ├── met_api.py          # Met Museum API wrapper
│   ├── embedder_OpenAI.py         # ChromaDB index builder + query
│   └── ranker_OpenAI.py           # OpenaI re-ranker (vision + text)
└── data/
    └── chroma_index/       # Persistent ChromaDB index (auto-created)
```

---

### 1. Clone and install

```bash
git clone https://github.com/yourusername/met-museum-rag
cd met-museum-rag

python -m venv venv
source venv/bin/activate

pip install -r requirements.txt
```

### 2. Set your OpenAI API key

```bash
export OPENAI_API_KEY=sk-....
```
Windows
```bash
set OPENAI_API_KEY=sk-...
```

Get your key at [OpenAI]((https://platform.openai.com/api-keys)).

### 3. Build the vector index *(one-time, ~2–5 min)*

```bash
python build_index.py
```

Fetches ~5,000 artworks from the Met API and saves a persistent ChromaDB index to `data/chroma_index/`. Only needs to run once.

### 4. Launch

```bash
streamlit run app.py
```

Opens at `http://localhost:8501`.

---

## Configuration

### Model selection (`src/ranker_OpenAI.py`)

```python
MODEL = "gpt-4.1"
MODEL = "gpt-4o"
MODEL = "gpt-4o-mini"
```

### Index size (`src/embedder_OpenAI.py`)

```python
model_name="text-embedding-3-large"
```

### Sidebar controls (runtime)

| Setting | Description |
|---|---|
| Results to return | Top-K shown (3–12) |
| Candidates to retrieve | ChromaDB results passed to OpenAI (20–100) |
| Enable vision ranking | Toggle image analysis on/off |

---

## Cost

| Component         | Cost     |
| ----------------- | -------- |
| Met API           | Free     |
| ChromaDB          | Free     |
| Streamlit         | Free     |
| OpenAI embeddings | very low |
| GPT re-ranking    | low      |

---

## Troubleshooting

| Issue                        | Fix                                    |
| ---------------------------- | -------------------------------------- |
| `ModuleNotFoundError: src.*` | run from root or `export PYTHONPATH=.` |
| `OPENAI_API_KEY not set`     | re-export key                          |
| `NoneType not iterable`      | use `obj.get("tags") or []`            |
| 403 errors (Met API)         | reduce concurrency + add delay         |
| empty index                  | rebuild                                |


---

## Roadmap

- [ ] CLIP embeddings for true multimodal retrieval (image + text in same vector space)
- [ ] Full 406k artwork index (overnight build)
- [ ] Image-based search (upload a photo, find visually similar artworks)
- [ ] Hybrid search: ChromaDB semantic + BM25 keyword (reciprocal rank fusion)
- [ ] Conversational refinement (*"show me darker ones"*, *"only 15th century"*)
- [ ] Deploy to Streamlit Cloud / HuggingFace Spaces
- [ ] Evaluation framework: Precision@K and NDCG on a curated test set

---

## Data

All artwork data is from [The Metropolitan Museum of Art Open Access](https://metmuseum.github.io/) dataset, released under [Creative Commons Zero (CC0)](https://creativecommons.org/publicdomain/zero/1.0/). Images are served directly from metmuseum.org.

---

## Author

**Deepali Balakrishna** · MSDS @ NYU  
[LinkedIn](https://www.linkedin.com/in/deepali-bk/) · [Portfolio](https://www.datascienceportfol.io/deepalibalakrishnate)
