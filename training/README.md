# Retriever training and evaluation

Fine-tuning and benchmarking of the scientific retriever used by SciBot (base model: `BAAI/bge-small-en-v1.5`). Both notebooks were run on a free Google Colab T4 GPU.

| Notebook | What it does |
|---|---|
| `scifact_finetune_and_eval.ipynb` | Loads SciFact (BEIR), runs the BM25 and off-the-shelf BGE-small baselines, fine-tunes BGE-small on the SciFact **train** split (MultipleNegativesRankingLoss, 4 epochs, batch 32, lr 2e-5, 20 warmup steps), saves the model as `scibot-sci-retriever`, repeats training with seeds 1, 2 and 3, and tests zero-shot on NFCorpus. |
| `scidocs_trec_covid_eval.ipynb` | Loads the saved `scibot-sci-retriever` (upload its zip) and compares it with the base model on SCIDOCS and TREC-COVID (documents truncated to 256 tokens). |

Run `scifact_finetune_and_eval.ipynb` first, top to bottom, with a GPU runtime. Cells depend on each other, so run them in order.

## Data
- Training: SciFact train split, 809 queries giving 919 (claim, abstract) pairs. The test split is never used for training.
- Held-out evaluation: SciFact test (300 queries), NFCorpus, SCIDOCS, TREC-COVID. All come from BEIR (`BeIR/*` on Hugging Face), downloaded by the notebooks.

## Results (saved in the notebook outputs)
| Benchmark | Metric | BGE-small | Fine-tuned |
|---|---|---|---|
| SciFact | Recall@5 | 0.764 | 0.817 (mean of seeds 1-3, std 0.007) |
| NFCorpus | MRR@10 | 0.529 | 0.550 |
| SCIDOCS | nDCG@10 | 0.206 | 0.201 |
| TREC-COVID | nDCG@10 | 0.798 | 0.744 |

BM25 on SciFact: Recall@5 0.621. The saved model is the single seed-42 run (Recall@5 0.824); 0.817 is the 3-seed mean.

The same query prefix (`Represent this sentence for searching relevant passages: `) is used in training and at search time.
