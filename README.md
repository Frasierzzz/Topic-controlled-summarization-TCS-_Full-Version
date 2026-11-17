# Topic-controlled-summarization-TCS-_Full-Version
Source code for the paper: "A Modular Data Augmentation Framework for Low-Resource Scientific and Biomedical Topic-Controlled Summarization."


This repository contains the code used in the manuscript submitted to *Scientific Reports*. It covers corpus construction and topic annotation, pairwise data augmentation with difficulty tiers, fine-tuning of T5-base, evaluation, prompted LLM baselines, and the additional experiments added during the revision.

## Contents

| File | Description |
|---|---|
| `code_for_manu.ipynb` | Full pipeline, organised in four parts (see below) |
| `requirements.txt` | Core libraries and the versions used for evaluation |
| `requirements_full.txt` | Complete list of packages in the Google Colab environment (`pip freeze`) |

## Data

The corpora (TLDR+ and PMC+), the augmented training sets, the test sets and the model outputs used to compute the reported results are available at Harvard Dataverse: https://doi.org/10.7910/DVN/WWXMUC

The science domain is built from SciTLDR (Abstract configuration). The biomedical domain is built from PubMed Central articles published in 2015 under CC BY or CC0 licences.

## Environment

The notebook was written for Google Colab with a GPU runtime.

- **Libraries.** The evaluation and the additional experiments were run with the versions in `requirements.txt` (Python 3.13). The models were trained earlier with the library versions available on Google Colab at that time.
- **API keys.** Keys are read from Colab Secrets (key icon in the left panel) and are never stored in the code:

| Secret | Used for |
|---|---|
| `WF_TOKEN` | Wikifier topic annotation (science domain) |
| `NCBI_API_KEY` | Fetching PubMed Central articles (optional; raises the request limit) |
| `HF_KEY` | Downloading Qwen3-Embedding-0.6B (second encoder) |
| `GEMINI_API_KEY`, `MISTRAL_API_KEY`, `CEREBRAS_API_KEY`, `GROQ_API_KEY` | Prompted LLM baselines |

- **MedCAT.** The biomedical topics use MedCAT 2.6.0 with the publicly released MedMentions model pack `medmen_wstatus_2021_oct`, without further fine-tuning. Download the model pack and set `MEDCAT_PACK` in Part 2.2.

## Notebook structure

| Part | Content |
|---|---|
| 0 · Common setup | Input construction, `.pth` training files, fine-tuning (`train_t5`), summary generation (`generate_summaries`), LLM client helpers |
| 1 · Science (TLDR+) | 1.1 corpus and Wikifier topics · 1.2 pairs, tiers and scales · 1.3 training · 1.4 test summaries · 1.5 evaluation · 1.6 LLM baselines |
| 2 · Biomedical (PMC+) | 2.1 corpus construction · 2.2 split, records and MedCAT topics · 2.3 pairs, tiers and scales · 2.4 training · 2.5 test summaries · 2.6 evaluation · 2.7 LLM baselines |
| 3 · Additional experiments | 3.1 second evaluation encoder · 3.2 size-matched random-mixing baseline · 3.3 statistical tests · 3.4 figure |

Run Part 0 first. Each later part can then be run on its own. Paths are set in the `CONFIG` cell at the start of each section.

## Where each result comes from

| Manuscript | Notebook section |
|---|---|
| Table 3 (datasets) | 1.1, 2.1, 2.2 |
| Table 4 and Supplementary Tables S1–S3 (augmentation and scale) | 1.5, 2.6 |
| Table 5 (difficulty tiers and random baseline) | 1.5, 2.6, 3.2 |
| Table 6 (LLM comparison) | 1.6, 2.7 |
| Supplementary Tables S5–S7 (normality, group and trend tests) | `per_seed.csv` and `per_seed_pmc.csv` from 1.5 and 2.6, analysed in IBM SPSS Statistics |
| Supplementary Table S8 (accepted section headings) | 2.1 (`clean_section`, `extract_intro_conclusion`) |
| Supplementary Table S9 (second encoder) | 3.1, 3.3 |
| Supplementary Table S10 (random baseline statistics) | 3.2, 3.3 |

## Reproducibility notes

- **Seeds.** Every configuration is trained with three seeds, tagged `000`, `100` and `200` (random seeds 14, 100 and 200). Reported values are means over the three seeds.
- **Pair sampling.** Pairs are drawn with NumPy's random generator. Re-running the data preparation gives pools with the same similarity distribution and tier shares, but not the identical pairs used to train the reported models; the exact training sets are in the Dataverse deposit.
- **Biomedical evaluation input.** `EVAL_CONTEXT = "abstract"` (Part 2.2) reproduces the validation and test sets used for the reported results, in which the whole abstract is the input. This makes the biomedical task partly extractive, as discussed in the Limitations of the manuscript. `EVAL_CONTEXT = "fulltext"` builds these sets in the same way as the training records.
- **Row order of result files.** Topic-alignment scores assume that each result file is in the same row order as its test file. Part 3.1 prints the Win Rate per seed; a value close to the chance level (50% in the biomedical domain) indicates a misaligned file. Such seeds can be excluded with `EXCLUDE_SEEDS` in Part 3.3.

## Citation

If you use this code, please cite:

> Yodthap, N., Bulathwela, S. & Intharah, T. A modular data augmentation framework for low-resource scientific and biomedical topic-controlled summarization. *Scientific Reports* (under review).

## Contact

Thanapong Intharah (corresponding author), Visual Intelligence Laboratory, Faculty of Science, Khon Kaen University, Thailand — thanin@kku.ac.th
