# 🏛 Met Museum Multimodal RAG

> Semantic search across 470,000+ artworks from The Metropolitan Museum of Art using natural language — powered by **Claude vision**, **ChromaDB**, and the **Met Open Access API**.

![Python](https://img.shields.io/badge/Python-3.10+-blue?style=flat-square&logo=python)
![Streamlit](https://img.shields.io/badge/Streamlit-1.35+-red?style=flat-square&logo=streamlit)
![ChromaDB](https://img.shields.io/badge/ChromaDB-0.5+-green?style=flat-square)
![Claude](https://img.shields.io/badge/Claude-Opus%204-purple?style=flat-square)
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
│   1. ChromaDB Retrieval         │  sentence-transformers (all-MiniLM-L6-v2)
│      Semantic similarity search │  ~5,000 artworks indexed locally
└──────────────┬──────────────────┘
               │  top-N candidates (object IDs)
               ▼
┌─────────────────────────────────┐
│   2. Met API Fetch              │  live REST API · no key required
│      Metadata + image URLs      │  collectionapi.metmuseum.org
└──────────────┬──────────────────┘
               │  enriched objects with images
               ▼
┌─────────────────────────────────┐
│   3. Claude Re-ranking          │  multimodal: reads text + images
│      Vision + text scoring      │  returns relevance score + explanation
└──────────────┬──────────────────┘
               │  top-K ranked results
               ▼
┌─────────────────────────────────┐
│   4. Streamlit Gallery          │  live images · score pills
│      Interactive display        │  detail expanders · Met links
└─────────────────────────────────┘
```

### Why this is a real RAG

| RAG Component | Role | Technology |
|---|---|---|
| **Retrieval** | Semantic similarity search over indexed artworks | ChromaDB + `all-MiniLM-L6-v2` embeddings |
| **Augmentation** | Live artwork metadata + images fetched per query | Met Museum Open Access API |
| **Generation** | Re-ranking with scores + curatorial explanations | Claude (vision + text) |

Traditional keyword search fails for prompts like *"melancholic women in Renaissance portraits"* — this pipeline understands semantic intent and visual content.

---

## Tech Stack

`Python` · `Streamlit` · `Anthropic Claude API` · `Claude Vision` · `ChromaDB` · `sentence-transformers` · `HNSW vector indexing` · `RAG` · `Met Museum Open Access API` · `REST API` · `concurrent.futures` · `base64 image encoding` · `semantic search` · `all-MiniLM-L6-v2` · `LLM re-ranking` · `prompt engineering` · `Pillow` · `requests`

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
│   ├── embedder.py         # ChromaDB index builder + query
│   └── ranker.py           # Claude re-ranker (vision + text)
└── data/
    └── chroma_index/       # Persistent ChromaDB index (auto-created)
```

---

## Quickstart

### 1. Clone and install

```bash
git clone https://github.com/yourusername/met-museum-rag
cd met-museum-rag

python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

pip install -r requirements.txt
```

### 2. Set your Anthropic API key

```bash
export ANTHROPIC_API_KEY=sk-ant-...   # Windows: set ANTHROPIC_API_KEY=...
```

Get your key at [console.anthropic.com](https://console.anthropic.com).

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

### Model selection (`src/ranker.py`)

```python
MODEL = "claude-opus-4-6"        # Best quality (~$0.05–0.15/search)
MODEL = "claude-sonnet-4-20250514"  # 10x cheaper, still excellent
```

### Index size (`src/embedder.py`)

```python
TARGET_ARTWORKS = 5000    # Increase for broader coverage (more build time)
```

### Sidebar controls (runtime)

| Setting | Description |
|---|---|
| Results to return | Top-K shown (3–12) |
| Candidates to retrieve | ChromaDB results passed to Claude (20–100) |
| Enable vision ranking | Toggle Claude image analysis on/off |

---

## Cost

| Component | Cost |
|---|---|
| Met Museum API | Free · no key · CC0 |
| ChromaDB | Free · runs locally |
| sentence-transformers | Free · runs locally |
| Streamlit | Free · runs locally |
| **Anthropic API** | ~$0.05–0.15/search (Opus + vision) |
| **Anthropic API** | ~$0.001/search (Sonnet, vision off) |

For development and demos, use Sonnet with vision toggled off — essentially free. Enable Opus + vision for showcasing the full multimodal pipeline.

---

## Troubleshooting

| Error | Fix |
|---|---|
| `ModuleNotFoundError` | Activate venv and re-run `pip install -r requirements.txt` |
| `ANTHROPIC_API_KEY not set` | Re-export the key — it resets when the terminal closes |
| `chromadb` version conflict | `pip install chromadb==0.5.0` |
| Port already in use | `streamlit run app.py --server.port 8502` |
| No results returned | Try broader keywords; some departments are sparsely indexed |

---

## Roadmap

- [ ] CLIP embeddings for true multimodal retrieval (image + text in same vector space)
- [ ] Full 406k artwork index (overnight build)
- [ ] Image-based search (upload a photo, find visually similar artworks)
- [ ] Hybrid search: ChromaDB semantic + BM25 keyword (reciprocal rank fusion)
- [ ] Conversational refinement (*"show me darker ones"*, *"only 15th century"*)
- [ ] Deploy to Streamlit Cloud / HuggingFace Spaces
- [ ] Evaluation framework: Precision@K and NDCG on a curated test set
- [ ] Claude image descriptions baked into index at build time

---

## Data

All artwork data is from [The Metropolitan Museum of Art Open Access](https://metmuseum.github.io/) dataset, released under [Creative Commons Zero (CC0)](https://creativecommons.org/publicdomain/zero/1.0/). Images are served directly from metmuseum.org.

---

## Author

**Deepali Balakrishna** · MSDS @ NYU  
[LinkedIn](https://www.linkedin.com/in/deepali-bk/) · [Portfolio](https://www.datascienceportfol.io/deepalibalakrishnate)
