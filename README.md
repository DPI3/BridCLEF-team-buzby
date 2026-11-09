# Team information
Team name: buzby
- Dénes Péter István - PO6MVA
- Szentpéteri Botond - GTRQBY
- Barbély László - TZEWBC
# BirdCLEF kaggle project
## Overview
Our project for the [BirdCLEF 2026](https://www.kaggle.com/competitions/birdclef-2026) Kaggle competition: recognizing
animal species (234 classes: birds, amphibians, insects, mammals, reptiles) in 5-second windows of field recordings.

Milestone I (data acquisition, exploration and preparation) is in
[`Milestone_1.ipynb`](Milestone_1.ipynb):
- **Part 1 (Sections 0-6):** data download, exploration and visualization.
- **Part 2 (Sections 7-12):** training / validation / test inputs and outputs. The inputs are embeddings of 5-second
  windows computed by the frozen, pretrained Google Perch v2 model; the outputs are 0/1 label vectors over the 234 species.

## Data source
- Competition data: [BirdCLEF 2026 on Kaggle](https://www.kaggle.com/competitions/birdclef-2026), downloaded with the
  Kaggle API (`kagglehub`). Training clips come from public sound archives (e.g. iNaturalist); each clip's archive,
  author, original URL and Creative Commons license are listed in `train.csv` (`collection`, `author`, `url`, `license`).
  Use of the data follows the competition rules.
- Pretrained model: [Google Perch v2](https://www.kaggle.com/models/google/bird-vocalization-classifier/tensorFlow2/perch_v2/2)
  (Kaggle Models).

# How to run
Both options need a Kaggle API token; Section 0 of the notebook explains how to get one.

## 1. Run in Google Colab
1. Open the notebook with the **Open in Colab** badge at its top.
2. Add a Colab secret named `KAGGLE_API_TOKEN` (key icon in the left sidebar) and turn on notebook access.
3. Uncomment `!pip install tinytag` in the first code cell (Colab does not include it).
4. For Part 2, choose a GPU runtime (Runtime → Change runtime type).
5. Run all cells. The data (~16 GB) is downloaded on the first run of each session.

## 2. Run locally
Requires [uv](https://docs.astral.sh/uv/) and Python 3.12 or 3.13 (TensorFlow does not support 3.14 yet; uv picks a suitable version).

1. Install uv (once):
   - macOS / Linux: `curl -LsSf https://astral.sh/uv/install.sh | sh`
   - Windows (PowerShell): `powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"`
   - or, with any existing Python: `pip install uv`
2. In the project folder, install the dependencies (creates `.venv` from `uv.lock`):
   ```
   uv sync
   ```
3. Create a text file named `.env` in the project folder containing this line (save it as UTF-8):
   ```
   KAGGLE_API_TOKEN=your_token_here
   ```

Open `Milestone_1.ipynb` in VS Code or Jupyter, select the `.venv` kernel and run all cells.
The data is downloaded into `data/` on the first run.

Part 2 needs a GPU for the full dataset. To try it on a laptop, set `ALLOW_CPU = True` (Part 2 settings cell) and
`PILOT_FILES_PER_SOURCE = 20` (Section 8).

# Repository files
| File | Description |
|---|---|
| `Milestone_1.ipynb` | Main Milestone I notebook: data download, exploration, visualization and data preparation (train / validation / test). |
| `data-exploration (13).ipynb` | Earlier Perch v2 pipeline notebook (also trains and evaluates a model); its data preparation part is merged into `Milestone_1.ipynb`. |
| `pyproject.toml`, `uv.lock` | Python dependencies (install with `uv sync`). |
| `.gitignore` | Keeps the data, `.env` and virtual environments out of git. |
| `data/` *(not in git)* | Downloaded competition data, metadata caches, and the prepared dataset in `data/prepared/`. |
| `.env` *(not in git)* | Your Kaggle token (`KAGGLE_API_TOKEN=...`). |
