# Setup — L09 — NLP, Embeddings & Semantic Search

> One-time environment setup. **If you already set up the `dsai-m3` environment for L01–L08, you're almost done — L09 needs `sentence-transformers >= 3.0`. If you created the environment from any lesson's `environment.yml`, it's already included; otherwise run `pip install sentence-transformers` once inside the activated environment. Then just clone this repo and pick the `dsai-m3` kernel.**

This guide gets you from "fresh machine" to "running the L09 notebooks". It takes about 15 minutes the first time.

---

## 1. Prerequisites

You need:

- **Git** — to clone this repository
- **Miniconda** (or Anaconda) — to create the Python environment. [Install Miniconda →](https://docs.conda.io/projects/miniconda/en/latest/)
- **VS Code** — with the Python and Jupyter extensions. [Download VS Code →](https://code.visualstudio.com/)

Check git and conda are working:

```bash
git --version
conda --version
```

---

## 2. Clone this repository

```bash
git clone https://github.com/flexfengfeng/6m-data-3.9-Natural-Language-Processing.git
cd 6m-data-3.9-Natural-Language-Processing
```

> **No local install wanted?** You can skip all of this and run every notebook in Google Colab instead — see the "Run in the cloud" section in [README.md](./README.md).

---

## 3. Create the conda environment

From inside the `6m-data-3.9-Natural-Language-Processing` folder:

```bash
conda env create -f environment.yml
```

This creates an environment called **`dsai-m3`** with Python, pandas, numpy, matplotlib, scikit-learn, sentence-transformers, and Jupyter. It takes 5–10 minutes.

> **Already have a `dsai-m3` environment from L01–L08?** You don't need to recreate it. Just confirm sentence-transformers is present:
>
> ```bash
> conda activate dsai-m3
> python -c "import sentence_transformers; print(sentence_transformers.__version__)"
> ```
>
> If that errors, run `pip install "sentence-transformers>=3.0"` once.

Activate the environment:

```bash
conda activate dsai-m3
```

---

## 4. First-run downloads (automatic)

- **Data (included in the repo):** `notebooks/data/northstar_catalogue.csv` (76 products, ~15 KB) and `notebooks/data/recipes.csv` (30 recipes, ~6 KB).
- **Models:** first run downloads `all-MiniLM-L6-v2` (~80 MB) from Hugging Face into `~/.cache/huggingface/`. `optional_extensions.ipynb` additionally downloads `paraphrase-multilingual-MiniLM-L12-v2` (~280 MB).

Everything in L09 runs comfortably on CPU — no GPU needed.

---

## 5. Open the project in VS Code

```bash
code .
```

When VS Code opens, it may prompt you to install recommended extensions (Python, Jupyter). Accept.

---

## 6. Select the `dsai-m3` kernel

1. Open `notebooks/01_monday_morning.ipynb`.
2. In the top-right of the notebook, click **Select Kernel**.
3. Choose **Python Environments → dsai-m3**.

If `dsai-m3` doesn't appear, restart VS Code and try again. If it still doesn't appear, run `conda env list` in a terminal to confirm the environment exists.

---

## 7. Smoke test

In `01_monday_morning.ipynb`, run the first cell (the imports). If it completes without errors, you're set.

If you see `ModuleNotFoundError`, the wrong kernel is selected — go back to Step 6. If only `sentence_transformers` is missing, see Step 3's note.

---

## Troubleshooting

**`conda: command not found`** → Miniconda isn't installed or isn't on your PATH. Reinstall Miniconda and restart your terminal.

**`environment.yml` solver hangs** → Try `conda env create -f environment.yml --solver=libmamba`. If that still hangs, delete the env (`conda env remove -n dsai-m3`) and retry.

**Kernel doesn't appear in VS Code** → Run `python -m ipykernel install --user --name dsai-m3` from inside the activated environment.

**Hugging Face download fails** → Check your network, then re-run the cell — downloads resume from the cache.

---

Once setup is done, head back to **[README.md](./README.md)** → Phase 1.
