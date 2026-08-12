# EDA-Schema Leaderboard — interaction mockup

A click-through prototype of the benchmark leaderboard for
[EDA-Schema-V2](https://arxiv.org/abs/2605.06952), built by the
[Drexel ICE Lab](https://drexel-ice.github.io/).

**Live:** _(add your URL here after first deploy)_

## What is real and what is not

| | Status |
|---|---|
| OpenROAD baselines, all 46 metrics × 4 PDKs × 5 stages | **Real** — transcribed from the [published Table 8](https://drexel-ice.github.io/eda-schema/) |
| Void cells (wirelength before placement) | **Real** — 40 cells |
| Saturated cells (baseline error 0 at global route) | **Real** — 132 cells |
| "No ± error" cells (MPE/MNE undefined at global route) | **Real** — 24 cells |
| Model names, scores, ranks, architectures, dates | **Synthetic placeholders** |

Model results are deterministic filler generated from a seeded hash, so the
numbers never change between reloads. They exist to exercise every UI path at
full scale before real submissions land.

## What it demonstrates

- **Matrix** — 12 tasks × 4 PDKs, stage selector, expand a task into its metric
  rows, five cell states each carrying a glyph so color is not the only signal
- **Cell page** — the OpenROAD baseline pinned above a ranked list of 18 models,
  filters, predicted-vs-actual and per-circuit charts
- **Model page** — architecture diagram with block height scaled to layer width,
  parameter counts, declared input features with a stage-legality badge, and the
  model's rank in every other metric of the same task

## Running locally

No build step, no dependencies.

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

## Theme

Mirrors the ICE Lab site (al-folio Jekyll theme): Roboto and Roboto Slab,
`--global-*` CSS custom property names, lowercase page titles and nav labels,
and a `[data-theme="dark"]` toggle. Drexel navy `#07294D` is used for links,
active state, and the footer; gold `#FFC600` appears only as chrome accent —
never on data, where it fails contrast.
