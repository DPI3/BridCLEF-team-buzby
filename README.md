# Team information
Team name: buzby
- Dénes Péter István - PO6MVA
- Szentpéteri Botond - GTRQBY
- Barbély László - TZEWBC
# BirdCLEF kaggle project
## Overview
Our project for the [BirdCLEF 2026](https://www.kaggle.com/competitions/birdclef-2026) Kaggle competition: recognizing
animal species (234 classes: birds, amphibians, insects, mammals, reptiles) in 5-second windows of field recordings.

Milestones I and II are combined in
[`Milestone_1.ipynb`](Milestone_1.ipynb):
- **Part 1 (Sections 0-6):** data download, exploration and visualization.
- **Part 2 (Sections 7-12):** training / validation / test inputs and outputs. The inputs are embeddings of 5-second
  windows computed by the frozen, pretrained Google Perch v2 model; the outputs are 0/1 label vectors over the 234 species.
- **Part 3 (Sections 3.1-3.7):** train and evaluate a multi-label attention MIL classifier on the prepared Perch embeddings.
  Each recording is a bag of windows. A shared window classifier and species-specific attention pooling produce bag-level predictions;
  padding is masked. Training uses binary cross-entropy, Adam, validation-based early stopping and a saved best checkpoint.

Part 3 creates its own group-held-out training and validation split from the Part 2 manifest and downloaded metadata.
Linked recordings stay together using recording IDs, repeated source URLs, available rounded coordinates and soundscape site/day metadata.
Part 2 test examples and all groups connected to them are excluded from model development. These metadata proxies reduce overlap but do not
guarantee independent environments. Validation reports ROC AUC, average precision and precision/recall/F1, including source and species breakdowns.
The reserved test set is not scored in Part 3.

Only the notebook is needed for submission: it installs missing packages and generates its split files itself.
No supplied split CSV, model weights or `assets` folder is required. The filename is retained so existing repository links keep working.

## Data source
- Competition data: [BirdCLEF 2026 on Kaggle](https://www.kaggle.com/competitions/birdclef-2026), downloaded with the
  Kaggle API (`kagglehub`). Training clips come from public sound archives (e.g. iNaturalist); each clip's archive,
  author, original URL and Creative Commons license are listed in `train.csv` (`collection`, `author`, `url`, `license`).
  Use of the data follows the competition rules.
- Pretrained model: [Google Perch v2](https://www.kaggle.com/models/google/bird-vocalization-classifier/tensorFlow2/perch_v2/2)
  (Kaggle Models).

# How to run
Both options need internet access, sufficient disk space for the downloaded data and generated embeddings, and a Kaggle API token.
Accept the competition rules on Kaggle with the same account first. Section 0 explains authentication.

## 1. Run in Google Colab
1. Open the notebook with the **Open in Colab** badge at its top.
2. Choose a GPU runtime (Runtime → Change runtime type) for the full Perch export in Part 2.
3. Add a Colab secret named `KAGGLE_API_TOKEN` and turn on notebook access, or enter the token in the hidden prompt when asked.
4. Run all cells from top to bottom. The first code cell installs missing packages. The data (~16 GB) is downloaded when not already available.
5. Part 3 uses the data and embeddings prepared earlier in the same notebook; it saves each training run in a separate results folder.

The Colab badge points to `main/Milestone_1.ipynb`. Until this branch is merged, it opens the previous version;
to review the pending version in Colab, open the notebook URL from the `Milestone_2` branch instead. After merging, the badge opens the updated notebook.

## 2. Run locally
1. Open `Milestone_1.ipynb` in VS Code or Jupyter with a Python 3.12 or 3.13 kernel.
2. Run all cells in order. The notebook installs missing packages into that kernel.
3. Enter your Kaggle token in the hidden prompt, or provide `KAGGLE_API_TOKEN` as an environment variable.
   An optional UTF-8 `.env` file containing `KAGGLE_API_TOKEN=your_token_here` is also supported; never commit this file.

If you use the repository's existing [uv](https://docs.astral.sh/uv/) environment, run `uv sync` and select its `.venv` kernel.
This is optional; the notebook bootstrap also installs packages missing from that environment.
Data is downloaded into `data/` and reused on subsequent runs when available.

Part 2 needs a GPU for the full dataset. To try it on a laptop, set `ALLOW_CPU = True` (Part 2 settings cell) and
`PILOT_FILES_PER_SOURCE = 20` (Section 8).
Pilot runs are for checking the pipeline; a very small dataset may not contain enough independent groups for the Part 3 split.

## Training outputs
By default, Part 3 creates a timestamped directory under `data/prepared/attention_mil_runs/`.
It saves the model, class mapping, settings, generated split manifests, training history, predictions and evaluation results.
These are generated outputs, not additional input files needed to run the notebook.

# Repository files
| File | Description |
|---|---|
| `Milestone_1.ipynb` | Combined Milestones I and II: data preparation, Perch features, attention MIL training and evaluation. |
| `pyproject.toml`, `uv.lock` | Optional repository environment (install with `uv sync`); the notebook installs missing packages itself. |
| `.gitignore` | Keeps the data, `.env` and virtual environments out of git. |
| `data/` *(not in git)* | Downloaded competition data, metadata caches, and the prepared dataset in `data/prepared/`. |
| `.env` *(not in git)* | Your Kaggle token (`KAGGLE_API_TOKEN=...`). |
