# Before Class — L09 — NLP & Embeddings

**Estimated time: ~30 minutes.** Complete this before class.

This is the simplest version of "show up prepared": watch a short intro, run one notebook, answer a few questions. You'll come to class having seen the idea in action.

| Step | Time | What you do |
|---|---|---|
| **0. Watch** | ~5 min  | Watch the [lesson intro video](https://youtu.be/uworo8tadFQ) |
| **1. Try it** | ~20 min | Open and run `notebooks/01_monday_morning.ipynb` |
| **2. Reflect** | ~5 min  | Three short questions below |

---

## Step 0 — Watch the intro video (~5 min)

▶️ **[L09 The Geometry of Meaning](https://youtu.be/uworo8tadFQ)**

A short orientation to the week: why keyword search fails on real catalogues, and how embeddings let Sarah build meaning-based search. Watch it before opening the notebook.

---

## Step 1 — Try it (~20 min)

Open **`notebooks/01_monday_morning.ipynb`** in VS Code with the `dsai-m3` kernel. Run every cell top to bottom. Read the markdown between cells. Don't skip any cell.

A customer types *"blue summer dress"* into NorthStar's search bar. We have ten dresses that fit — none of them came up. The notebook walks Sarah through keyword search, watches it fail on synonyms (*frock* ≠ *dress*), and motivates the embedding-based fix you'll see in class.

If this is your first time running a notebook in this repo, see [setup.md](./setup.md) once — you only need to do this for the first lesson.

---

## Step 2 — Quick reflection (~5 min)

Write a sentence or two for each. You can scribble in a notebook, in a journal, or just hold the answer in your head — what matters is that you *tried*.

**Q1. Five ways to describe a summer dress without using the word *dress*.**

Write three of them. (This is exactly the synonym problem keyword search can't handle.)

**Q2. Stemming and stop-words help — but only a bit.**

Why can't lexical tricks (stem *dresses* → *dress*) solve the *frock* → *dress* gap?

**Q3. Search a real site for *"comfortable office shoes"*.**

Does the search seem to know what you mean, or is it just keyword-matching? How can you tell?

---

## Bring to class

- Your answer to Q2.
- One search problem from your own work or life where keyword search frustrates you.

---

**Want to go deeper before class?** See **[reference.md](./reference.md) → *Further reading & watching*** at the bottom — videos and recommended readings that used to live in this guide.

**Ran out of time?** Doing just Step 1 (running the notebook) is enough. The class builds on having felt what the lesson teaches; the reflection questions get re-asked live.
