# Wildfire Detection (Sentinel-2)

[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![PyTorch](https://img.shields.io/badge/PyTorch-ResNet-ee4c2c.svg)](https://pytorch.org/)
[![Sentinel-2](https://img.shields.io/badge/data-Sentinel--2-2ecc71.svg)](https://sentinel.esa.int/web/sentinel/missions/sentinel-2)
[![License: MIT](https://img.shields.io/badge/license-MIT-lightgrey.svg)](LICENSE)

Build a **3-class** satellite dataset (**fire** / **no_fire** / **burn_scar**) from [FIRMS](https://firms.modaps.eosdis.nasa.gov/) + Sentinel-2, then train ResNet classifiers on RGB or 6-band patches.

![Three example patches](data/figures/report_three_examples.png)

## Features

- FIRMS → Sentinel-2 candidate matching and patch download
- Quality filters: cloud / water / valid pixels + thermal (B12) thresholds
- Geographic train/val/test split (avoids spatial leakage)
- ResNet training for RGB and 6-band inputs (MPS / CUDA auto-detected)

## Setup

```bash
# with uv (recommended)
uv sync

# or pip
pip install -e .
```

Create a `.env` with Sentinel Hub credentials:

```bash
SH_CLIENT_ID=...
SH_CLIENT_SECRET=...
```

## Pipeline

```bash
python scripts/build_candidates.py   # match FIRMS to S2 catalog
python scripts/download_dataset.py   # download patches + metadata
python scripts/prepare_splits.py     # filter, balance, geographic split
```

## Train

```bash
# RGB
python models/train_resnet.py

# 6-band: set EXPERIMENT="all" in models/train_resnet.py, then rerun
```

Checkpoints land in `models/checkpoints/`.

## Data

| Item | Detail |
|------|--------|
| Splits | `data/splits/` — train / val / test |
| Bands | B02, B03, B04, B08, B11, B12 |
| Classes | `fire`, `no_fire`, `burn_scar` |

## Project layout

```
scripts/
  build_candidates.py   # FIRMS ↔ S2 matching
  download_dataset.py   # patch download
  prepare_splits.py     # filter + balance + split
  map_fires.py          # map helpers
models/
  dataset.py            # PyTorch dataset + transforms
  train_resnet.py       # RGB / 6-band ResNet training
data/figures/           # example visuals
```

## License

MIT — see [LICENSE](LICENSE).
