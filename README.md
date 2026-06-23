# TF-IDF vs BM25 — A Component-by-Component Study

**Languages:** English | [Русский](README.ru.md)

A single Jupyter notebook that dissects the mechanics of **TF-IDF** and **BM25**
and compares them on a real retrieval dataset — [NFCorpus](https://www.cl.uni-heidelberg.de/statnlpgroup/nfcorpus/)
from the [BEIR](https://github.com/beir-cellar/beir) benchmark.

The goal is **not** a generic ML benchmark. It is a careful breakdown of *each*
component of the two scoring formulas — with plots, intuition, and conclusions:

- **TF saturation** — why raw term frequency over-rewards "term spam"
- **Document length normalization** — penalizing long documents fairly
- **IDF smoothing** — handling rare and very common terms
- **BM25 hyperparameters** `k1` and `b` — a grid over synthetic and real data

## What's inside

The notebook `tfidf_vs_bm25.ipynb` is organized into 11 sections. Each section is
one H2 heading → code → a final **"Вывод:" (Conclusion)** cell.

1. Introduction and problem statement
2. Toy corpus and baseline vectorizations
3. TF: term-frequency saturation (TF-spam)
4. Document length normalization
5. IDF: smoothing, rare and frequent terms
6. BM25 hyperparameters: grid over `k1` and `b` (synthetic)
7. Loading NFCorpus
8. Indexing and ranking on NFCorpus
9. Quality evaluation: Recall@k, MRR, nDCG@k
10. Hyperparameter impact on NFCorpus
11. Final comparison and overall conclusion

## Setup

The project uses a **local virtual environment**. The global Python is left untouched.

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
python -m ipykernel install --user --name tfidf_bm25_venv
```

Then launch Jupyter and open the notebook:

```bash
jupyter notebook tfidf_vs_bm25.ipynb
```

Select the `tfidf_bm25_venv` kernel.

## Data

NFCorpus is **downloaded from within the notebook** (Section 7) — you do not need
to fetch it manually:

```python
from beir import util
from beir.retrieval.evaluation import GenericDataLoader

url = "https://public.ukp.informatik.tu-darmstadt.de/thakur/BEIR/datasets/nfcorpus.zip"
data_path = util.download_and_unzip(url, "datasets")
corpus, queries, qrels = GenericDataLoader(data_path).load(split="test")
```

The dataset (~3.6k documents) and the `datasets/` folder are **not committed** to
the repository — they are reproduced at runtime.

> **Note on `pytrec_eval`:** if `from beir.retrieval.evaluation import EvaluateRetrieval`
> fails to import (a common `pytrec_eval` build issue), the notebook falls back to
> manual NumPy implementations of Recall@k, MRR and nDCG@k.

## Reproducing

For a clean end-to-end run, use **"Restart Kernel and Run All"**. The whole
notebook should execute without errors in a few minutes on the ~3.6k documents.

## Project layout

```
tfidf_bm25/
├── tfidf_vs_bm25.ipynb   # the study (main artifact)
├── requirements.txt      # pinned dependencies
├── CLAUDE.md             # project notes / working agreement
├── LICENSE               # MIT
└── README.md / README.ru.md
```

## License

Released under the [MIT License](LICENSE).
