# EDA-Schema Leaderboard

Interaction prototype for the [EDA-Schema-V2](https://arxiv.org/abs/2605.06952)
benchmark, styled to match the [ICE Lab site](https://drexel-ice.github.io/).

Three linked pages. No build step, no dependencies, no backend.

| Page | What it does |
|---|---|
| `index.html` | Matrix of 46 metric rows × 5 stages × 4 PDKs. Click a cell for rankings, a rank matrix, a contextual problem breakdown, and five chart views. |
| `explorer.html` | Any submitted model: 3D unit view, vertical op graph, weight matrices, and the evidence that these checkpoints never trained. |
| `playground.html` | Pick a problem, choose which schema attributes feed in, shape a network, train it in-browser, try to beat OpenROAD. |

## The flow

```
matrix → cell → About (the problem, this stage, this PDK)
              → Rank matrix (every model, every metric)
              → a model → explorer → playground
```

Task, PDK, stage, model and circuit travel in the URL when you leave the matrix,
so `explorer.html` and `playground.html` are deep-linkable. The matrix itself
keeps its state in memory, so a reload lands back on the full grid.

## Real vs synthetic

| | Status |
|---|---|
| OpenROAD baselines — 46 metrics × 4 PDKs × 5 stages | **Real**, from the [published Table 8](https://drexel-ice.github.io/eda-schema/) |
| Void (40), saturated (132), no-± (24) cells | **Real** |
| Circuit characteristics, clock periods, parameter sweep, QoR ranges | **Real** — Tables 2, 4, 5, 6 |
| Parameter–metric correlations | **Reproduced**, mean error 0.020 across 80 published coefficients |
| Model names, scores, ranks, weights | **Synthetic**, seeded and deterministic |
| Per-instance feature values | **Modelled** until the dataset Parquet is reachable |
| Design-stage illustrations | **Illustrative renderings**, not dataset images |

Every page carries a banner saying so.

## Running locally

```bash
python3 -m http.server 8000
```

## Deploying

Push to `main`. The included workflow provisions GitHub Pages and deploys.
If it fails on a fresh repo, set **Settings → Pages → Source → GitHub Actions**.

## Verified

```
metric rows        46        cells      920
void               40        live       880
saturated         132        no ± error  24
live combos       232        openable cells opened  232 / 232
About renders     240 / 240 with zero failures
baselines match Table 8 across all 80 non-void, non-saturated cells
```
