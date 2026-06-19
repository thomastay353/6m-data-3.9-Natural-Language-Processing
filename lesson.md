# Lesson — L09 NLP, Embeddings & Semantic Search

> **Chapter 9 of the NorthStar Retail story.** *Sarah Chen · Customer Experience Analyst · January 2023.*
> The L08 auto-tagger shipped on Friday. Monday morning, an angry email lands: a shopper typed *"blue summer dress"* into NorthStar's search bar. The catalogue has ten dresses that fit. None came up — the descriptions say *frock*, *sundress*, *gown*. Keyword search breaks on synonyms.
> This lesson is how Sarah replaces it.

This document is a **short reference** — the lesson itself is taught in the notebooks. Read it for orientation before class, then come back to it for the takeaways, the validation checklist, the review questions, and the course map.

---

## How L09 is taught

| Stage | Where to go |
|---|---|
| **Pre-class** | `pre-class.md` + `notebooks/01_monday_morning.ipynb` |
| **In-class — Part 1: Words as vectors** | `notebooks/02_words_to_vectors.ipynb` |
| **In-class — Part 2: Pretrained sentence embeddings** | `notebooks/03_pretrained_embeddings.ipynb` |
| **In-class — Part 3: Semantic search engine** | `notebooks/04_semantic_search.ipynb` |
| **Self-study** | `notebooks/assignment.ipynb` + `notebooks/optional_extensions.ipynb` |
| **Reference & review** | This document |

The notebooks are the spine. Run them in order. Come back here for the consolidated takeaways and the review questions.

---

## Overview

NorthStar's keyword search misses three of ten relevant dresses for the query *"blue summer dress"* — the descriptions use *frock*, *gown*, *sundress* instead, and no amount of stemming or stop-word stripping closes that gap because **the problem is semantic, not lexical**. By Friday Sarah will hold three new tools: a built-from-scratch understanding of **why one-hot and bag-of-words representations cannot express meaning**, hands-on use of **pretrained sentence-transformers** (`all-MiniLM-L6-v2`) that map text to 384-dim vectors where geometry encodes meaning, and a working **top-K semantic search engine** benchmarked head-to-head against TF-IDF on a labelled query set. The same retrieval pattern returns in L10 as the *R* in RAG.

---

## Key takeaways

1. **Keyword search fails on synonyms because tokens are treated as identities, not meanings.** *Frock*, *gown*, and *sundress* are different tokens; to a keyword matcher they are as unrelated as *frock* and *spaceship*. Stemming and stop-words help marginally; the underlying limitation is fundamental.
2. **One-hot vectors and bag-of-words are equidistant on synonyms.** Cosine similarity between any two distinct one-hot vectors is exactly 0. Bag-of-words inherits the same blindness — it only matches on shared content words.
3. **An embedding is a dense vector where geometric distance encodes semantic distance.** Numbers in each dimension have no intrinsic meaning; what matters is that semantically similar inputs end up close. The distributional hypothesis (*you shall know a word by the company it keeps*) is the training signal.
4. **Cosine similarity is a ranking signal, not a percentage.** Modern sentence-transformer cosines mostly live in `[0.2, 0.7]`, and the floor is **not** zero: with `all-MiniLM-L6-v2`, even unrelated pairs like *dress*/*spaceship* score ~0.25 — about the same as the *frock*/*dress* synonym. So a single pairwise cosine means little on its own; a 0.25 is **not** "25% similar" and must not be compared against an imagined 0.0 baseline. Read cosines as a *ranking within a query*, not a score out of 1.
5. **Pretrained sentence-transformers are the default starting point — you do not train from scratch.** `all-MiniLM-L6-v2` (22M params, 384-dim output, ~80 MB) was trained on >1B sentence pairs. You call `.encode()` and store the result; the model does the heavy lifting.
6. **Semantic search is two functions: embed-the-corpus-once + cosine-against-the-query.** No labels, no fine-tuning, no training loop. Embed the catalogue offline (seconds per 1000 products), embed the query at request time, return top-K by cosine similarity. The infrastructure is trivial.
7. **Evaluate retrieval with top-1 and top-K accuracy against a labelled ground-truth set.** On NorthStar's 8-query benchmark, keyword search hits 5/8 top-1, TF-IDF hits 4/8, semantic search hits 6/8 top-1 and 7/8 top-5. Without a ground-truth set you have no way to know if a change made search better or worse.
8. **Production search is hybrid, not embeddings-only.** TF-IDF beats embeddings on exact-token queries (product codes, brand names). Structured filters (category, price, colour) handle hard constraints. The standard pattern is: union of TF-IDF + semantic candidates → structured filters → re-rank by score blend, popularity, recency.

---

## Validating a semantic-search system before shipping — a checklist

Before you replace keyword search with embeddings in production, run it through this three-step check:

1. **Do you have a labelled ground-truth query set?** A handful of (query, expected-product) pairs is the minimum. Without it you cannot measure top-1 or top-K accuracy, and "it feels better" is not an answer Marcus will accept. NorthStar's 8-query benchmark in `ground_truth.json` is the pattern — small, hand-curated, representative of real customer language.
2. **Did you measure precision@k AND inspect the failures?** Headline accuracy hides failure modes. The 'blue summer dress' miss in NB 03 — where the model returned a *linen shirt* at rank 1 — is exactly the kind of weighting bug that a top-1 number alone would not surface. Always read the actual top-5 for the queries you got wrong.
3. **Have you planned for the exact-match and stale-embedding cases?** Product codes ("P0042") and brand names need exact matching; route them to TF-IDF, not embeddings. When merchandisers rewrite a description, the cached embedding goes stale until re-encoded; schedule nightly re-embedding of changed rows.

Skip any of these and you ship a system that *feels* smarter but quietly regresses on the queries that pay the bills.

---

## Check your understanding

Work through these after finishing the three Part notebooks. Attempt each question on your own first.

### Part 1 — Words as vectors

**Q1 — Why can't bag-of-words match synonyms?** A shopper types *"blue summer dress"*. The catalogue's Lila Floral Sundress is described as *"lightweight floral frock perfect for warm summer days"*. Explain in one sentence why a bag-of-words representation cannot rank this product highly.

> **Sample answer:** Bag-of-words represents each document as a vector of word counts indexed by a fixed vocabulary; *frock* and *dress* occupy different indices, so the query vector and the product vector share only the "summer" dimension and the cosine is dominated by that single overlap. The representation has no notion that *frock* and *dress* refer to the same thing — they are as orthogonal as *frock* and *spaceship*.

**Q2 — One-hot equidistance.** In NB 02 the cosine between `onehot("frock")` and `onehot("dress")` was 0.000, identical to the cosine between `onehot("frock")` and `onehot("spaceship")`. Why?

> **Sample answer:** A one-hot vector is all zeros except a single 1 at the word's index. The dot product of two one-hot vectors for *distinct* words is zero (they have no overlapping non-zero positions), so the cosine is zero regardless of how semantically related the words are. The representation captures identity, not meaning — every pair of distinct words is equidistant.

### Part 2 — Pretrained sentence embeddings

**Q3 — Read the cosine.** `all-MiniLM-L6-v2` returns cosine(*frock*, *dress*) = 0.249. A colleague says: "Only 25% similar — that's not very high." Are they reading it correctly?

> **Sample answer:** No. Sentence-transformer cosines for this model mostly live in the range [0.2, 0.7], and the floor is not 0 — with `all-MiniLM-L6-v2` even unrelated pairs (e.g. *dress*/*spaceship* ≈ 0.257) score around 0.25, roughly the same as the *frock*/*dress* synonym (0.249). So a 0.25 is **not** "25% similar", and a single pairwise cosine tells you very little by itself. The right way to use cosines is to *rank* candidates within a query against the whole corpus — where the relevant items aggregate enough signal to rise to the top — not to read one pair as a percentage out of 100%.

**Q4 — When is averaging word vectors not good enough?** Why does NB 03 use a sentence-transformer rather than just averaging word embeddings across the sentence?

> **Sample answer:** Averaging word vectors throws away word order and uses uniform weights, so *"dog bites man"* and *"man bites dog"* get identical embeddings, and high-information words are diluted by stop-words. A sentence-transformer is trained end-to-end to produce a single vector that captures the meaning of the whole sentence, including word order and emphasis. Averaging is a reasonable cheap baseline; the transformer is the default for any real application.

### Part 3 — Semantic search engine

**Q5 — Precision@k vs top-1.** On NorthStar's 8-query benchmark, the semantic engine gets top-1 = 6/8 but top-5 = 7/8. Which metric should Sarah report to Marcus, and what does each tell you?

> **Sample answer:** Report both. Top-1 accuracy is the strict measure — how often the most relevant product comes up first — and is the metric the customer experiences when they only look at the top result. Top-5 is the more forgiving measure that reflects the realistic browsing pattern of scrolling through a handful of options; it also tells you whether the right product is at least *on the page*. The gap between them (7/8 minus 6/8 here) is the headroom that re-ranking with popularity or structured filters could recover without a better embedding model.

**Q6 — When does TF-IDF beat embeddings?** Give one query type where TF-IDF will reliably outperform semantic search, and one where it will reliably lose.

> **Sample answer:** TF-IDF wins on exact-token queries: product codes ("P0042"), brand names, SKUs, exact product titles ("Marina Wrap Dress"). The literal-token match is precisely what TF-IDF is built for. TF-IDF loses on synonym-heavy or paraphrased queries ("warm winter jumper" when the catalogue calls it a *cable knit pullover*, or "blue summer dress" when descriptions say *frock*), because it has no notion that those tokens refer to the same thing. Production search uses both and merges the candidate lists.

**Q7 — Stale embeddings.** A merchandiser rewrites the description of three products on Wednesday afternoon. What goes wrong if you do nothing, and what is the standard fix?

> **Sample answer:** The cached embeddings for those three products were computed from the *old* description text; until you re-encode them, semantic search ranks them against query embeddings using a representation that no longer matches what's on the product page. Customers can search using language from the new description and never find the product, or worse, find it for the wrong reasons. The standard fix is a nightly re-embedding job that re-encodes any product whose `updated_at` timestamp changed in the last 24 hours, plus a small near-real-time job for newly added products.

---

## Where L09 fits in the course

L09 is the last "build a focused ML system" lesson — L10 opens the black box of the architecture that produces these embeddings and uses retrieval as a component of a larger system.

| Lesson | How L09 shows up |
|---|---|
| **L10 — Transformers & GenAI** | The sentence-transformer in NB 03 is a transformer encoder — L10 explains what attention is doing inside it. The semantic search engine from NB 04 returns directly as the **R in RAG (Retrieval-Augmented Generation)**: embed a knowledge base, retrieve top-K by cosine, feed the chunks into an LLM prompt. The evaluation discipline (ground-truth queries, precision@k) carries forward as the only honest way to measure an LLM pipeline. |

---

> *"OK, the search engine works. But how does that little model actually understand that *frock* and *dress* are similar? And can we use the same kind of model to build a chat assistant that answers customer questions about our products?"* — Marcus, after Sarah's Friday demo.
>
> Those questions — *what's inside the embedding model?* and *how do we build a chat assistant on top of it?* — are the engine of **L10 (Transformers & GenAI)**.
