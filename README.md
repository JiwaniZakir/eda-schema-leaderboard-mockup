# EDA-Schema Leaderboard — interaction mockup

Click-through prototype for the [EDA-Schema-V2](https://arxiv.org/abs/2605.06952)
benchmark, styled to match the [ICE Lab site](https://drexel-ice.github.io/).

## Layout

- **Columns are design stages**, so reading left to right shows the OpenROAD
  estimate converging toward the final value.
- **Three multi-select filters** — PDKs, stages, tasks — each with all/none.
  Selecting all 4 PDKs and all 5 stages reproduces Table 8: 20 data columns.
- **All 46 metric rows always visible**, grouped by task, with Task and Metric
  pinned while the grid scrolls horizontally.
- **Legend sits above the grid** so the colour key is read before the data.

## Theme provenance

Matched against `drexel-ice/drexel-ice.github.io` (al-folio):

| Element | Source |
|---|---|
| Roboto + Roboto Slab + Material Icons | exact font URL from `_config.yml` |
| `--global-*` token names | al-folio `_themes.scss` convention |
| `max_width: 930px` | `_config.yml`; the data grid uses `.container--wide` |
| Dark mode `#1c1c1d` / `#e8e8e8` | al-folio `$grey-color-dark` / `$grey-color-light` |
| Scroll progress bar, back-to-top, fixed navbar | `enable_progressbar`, `back_to_top`, `navbar_fixed` |
| Footer text | `footer_text` in `_config.yml` |

Drexel navy `#07294D` is used for links, active state and footer; gold `#FFC600`
only as chrome accent — never on data, where it fails contrast.

## Real vs synthetic

| | Status |
|---|---|
| OpenROAD baselines, 46 metrics x 4 PDKs x 5 stages | **Real**, from the [published Table 8](https://drexel-ice.github.io/eda-schema/) |
| Void (40), saturated (132), no-± (24) cells | **Real** |
| Model names, scores, ranks, architectures | **Synthetic**, seeded and deterministic |

## Running locally

```bash
python3 -m http.server 8000
```
