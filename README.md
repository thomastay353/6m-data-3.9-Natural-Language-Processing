# L09 — NLP, Embeddings & Semantic Search

> **Module 3 · Machine Learning & GenAI · Lesson 9 of 10**

## The story so far

Sarah's catalogue auto-tagger from L08 is in production. Merchandisers are confirming the top-3 predictions, the data pipeline is collecting corrections, and the model is improving week over week. Marcus is happy.

Then a complaint lands in his inbox from the customer-experience team:

> *"A shopper typed 'blue summer dress' into our search bar. We have **ten** dresses that fit that description — and zero of them came up. Our search is keyword-only. The customer left."*

Sarah opens the catalogue, runs a quick check, and confirms the problem: the most relevant dress is called "Lila Floral Sundress" — its description says **"lightweight floral frock"**, not "summer dress". Keyword search misses it. So do the others, each described with different words for the same concept.

This is a **semantic search** problem. We need a model that understands that *frock*, *sundress*, and *dress* mean similar things, and that *floral* and *summer* are co-related concepts.

The tool for the job is **word and sentence embeddings** — turning text into vectors so we can compute meaning-based similarity. By the end of L09 you will have shipped a working semantic search engine.

## What you'll learn

By the end of L09 you will be able to:

1. **Explain why keyword search fails** on real product catalogues (synonyms, paraphrases, typos, intent).
2. **Tokenise text** the way a modern model does, and understand the trade-offs of word vs subword tokens.
3. **Reason about word vectors** — what a 384-dim sentence embedding actually represents, and how cosine similarity gives you meaning.
4. **Use a pretrained sentence-transformer** (`all-MiniLM-L6-v2`) to embed an entire catalogue in seconds.
5. **Build a working semantic search engine** with top-K retrieval, compare it head-to-head against TF-IDF, and ship it as a function.

## Run in the cloud (no setup) — Google Colab

No laptop GPU or local install needed — these notebooks run on Colab's free CPU (the pretrained model only does fast inference, not training). Each notebook installs `sentence-transformers` on first run and loads its data straight from GitHub, so there's nothing to upload.

| Notebook | Open |
|---|---|
| 01 · Monday morning (pre-class) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/flexfengfeng/6m-data-3.9-Natural-Language-Processing/blob/main/notebooks/01_monday_morning.ipynb) |
| 02 · Words to vectors | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/flexfengfeng/6m-data-3.9-Natural-Language-Processing/blob/main/notebooks/02_words_to_vectors.ipynb) |
| 03 · Pretrained embeddings | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/flexfengfeng/6m-data-3.9-Natural-Language-Processing/blob/main/notebooks/03_pretrained_embeddings.ipynb) |
| 04 · Semantic search engine | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/flexfengfeng/6m-data-3.9-Natural-Language-Processing/blob/main/notebooks/04_semantic_search.ipynb) |
| Assignment · Recipe search | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/flexfengfeng/6m-data-3.9-Natural-Language-Processing/blob/main/notebooks/assignment.ipynb) |
| Optional extensions | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/flexfengfeng/6m-data-3.9-Natural-Language-Processing/blob/main/notebooks/optional_extensions.ipynb) |

> Prefer a local install (VS Code + conda)? See [setup.md](setup.md). The notebooks detect whether they're local or on Colab and load data accordingly — the same file runs in both.

## Phase 1 — Pre-class (≈ 30 min)

- Watch the [lesson intro video](https://youtu.be/uworo8tadFQ) (~5 min)
- Read [pre-class.md](pre-class.md)
- Watch the linked Jay Alammar embeddings primer
- Run [notebooks/01_monday_morning.ipynb](notebooks/01_monday_morning.ipynb) — feel the keyword-search failure mode firsthand

## Phase 2 — In-class (≈ 90 min lecture + 90 min code-along)

- Concept walkthrough: instructor uses the [**interactive key-concepts walkthrough →**](https://su-ntu-ctp.github.io/s6m-data-3.9-Natural-Language-Processing/) (revisit any time)
- **Short reference & review →** [**lesson.md**](./lesson.md) (overview, key takeaways, validation checklist, 7-question review, course map to L10)
- Code-along notebooks (in order):
  - [02_words_to_vectors.ipynb](notebooks/02_words_to_vectors.ipynb) — one-hot encoding, bag-of-words, the embedding intuition
  - [03_pretrained_embeddings.ipynb](notebooks/03_pretrained_embeddings.ipynb) — `SentenceTransformer` + cosine similarity
  - [04_semantic_search.ipynb](notebooks/04_semantic_search.ipynb) — build a search engine, beat TF-IDF

## Phase 3 — Post-class (self-study, optional)

- [assignment.ipynb](notebooks/assignment.ipynb) — Build a semantic search engine for a different corpus (news headlines)
- [optional_extensions.ipynb](notebooks/optional_extensions.ipynb) — TF-IDF vs embeddings deep-dive, embeddings for classification, multilingual queries

## Files

| Path | Purpose |
|------|---------|
| `README.md`              | This file — orientation |
| `setup.md`               | Environment install (`pip install sentence-transformers`) |
| `pre-class.md`           | ~30-min self-study before class |
| `lesson.md`              | Short reference: overview, takeaways, validation checklist, review Q&A, course map |
| `reference.md`           | Glossary of 22 NLP/embedding terms |
| `environment.yml`        | Conda env spec |
| `docs/index.html`        | Interactive key-concepts walkthrough (GitHub Pages) |
| `notebooks/`             | 4 in-class NBs + assignment + extensions + data |

---

**Bridge to L10:** Embeddings are the *representation*. But where do they come from? How does a model "understand" that *frock* and *dress* are related? The answer is **transformers and attention** — the architecture behind every modern NLP and GenAI model. L10 opens the black box.
