# EDA-Schema Leaderboard

Static, single-file interaction mockup of the EDA-Schema-V2 benchmark leaderboard for the Drexel ICE Lab.
`index.html` is the entire app: markup, CSS and JS inline, no build step, no dependencies, no framework.

`README.md` covers what the UI does, the theme provenance table, and which numbers are real.
Read it before changing behaviour; this file covers only what the code and README do not show.

## Commands

- Serve locally: `python3 -m http.server 8000`
- Deploy: merging to `main` runs `.github/workflows/deploy.yml`, which publishes the repo root to Pages
- Live: https://jiwanizakir.github.io/eda-schema-leaderboard-mockup/

There is no test suite, no linter and no package manager.
Verification is driving the page in a browser (see Verifying a change).

## Gotchas

### theme-drexel-ice.css is a hand-maintained duplicate

`theme-drexel-ice.css` is byte-identical to the first CSS block inside index.html's `<style>` tag, up to the `/* ===== mockup banner ===== */` marker.
`index.html` does not load it.
It exists as a standalone export so the lab can drop it into their al-folio site's `static/css/` without renaming a single token.

Editing either copy silently desynchronises them.
Change both, then confirm they still match:

```bash
sed -n '/^<style>$/,/^\/\* ===== mockup banner ===== \*\/$/p' index.html | sed '1d;$d' > /tmp/inline.css
diff <(sed 's/[[:space:]]*$//' /tmp/inline.css) <(sed 's/[[:space:]]*$//' theme-drexel-ice.css)
```

Only a single trailing blank line should differ.

### BASE is the only real data

The `BASE` object is transcribed from the published Table 8 and must not be edited to make the UI look better.
Everything else on the page (model names, scores, ranks, architectures, chart point positions) is generated from `H()`, an FNV-1a hash seeded on the cell coordinates.

That makes the mockup deterministic: the same cell shows the same fake number on every reload.
Changing `H()`, the `NM` model-name list, or any key passed to `H()` reshuffles every synthetic number on the site at once.

Invariants worth preserving, all derivable from `BASE`: 12 tasks, 46 metric rows, 920 cells at 4 PDKs x 5 stages, of which 40 are void, 132 saturated and 24 no-±.

### BASE uses sentinel values, not nulls everywhere

| Sentinel | Means | Renders as |
|---|---|---|
| `null` | not estimable at this stage (wirelength before placement) | `n/a`, greyed, not clickable |
| `-999` | MPE/MNE undefined, no ± error at global route | `no ±` |
| `99999` | MAPE overflow | `>10000%` |
| `-2` | R² floor | `<-1` |

Any new numeric code path must handle all four before doing arithmetic.
`fmt()` and `state()` are the two places that decode them.

### Higher-is-better metrics invert every comparison

`HIGHER = ["R²", "TPR", "TNR"]`.
For those, `better()` flips direction, `modelVal()` extrapolates toward a ceiling instead of scaling down, and saturation means `>= 1` or `>= 100` rather than `== 0`.
This is the easiest thing to break when touching ranking, sorting or cell-state code.
Test a change against both a lower-is-better metric (MAE) and a higher-is-better one (R²).

### Navigation is not routed

`show()` toggles `.hidden` across three sections.
There is no URL state, so a reload always lands on the matrix and drill-down views cannot be linked to.
`404.html` is a meta-refresh back to `/`, and `.nojekyll` stops Pages running Jekyll over the repo.

## Design rules that are load-bearing

These are accessibility guarantees, not preferences.
Do not trade them away for a cleaner look.

- Every cell state carries a glyph (`✓`, `—`, `◆`, `n/a`) so colour is never the only signal.
- Drexel gold `#FFC600` is chrome accent only and never touches data; it is 1.4:1 on white.
- Drexel navy `#07294D` is links, active state and footer only. The data palette is deliberately non-brand so the heatmap stays readable.
- Every data foreground/background pair clears 4.5:1 on its own ground, in both themes.
- Dark mode is a full second theme under `html[data-theme="dark"]`, not a filter. Anything with a hardcoded colour needs an explicit dark override, including elements nested inside already-overridden parents.
- All motion is gated behind `prefers-reduced-motion: reduce`.

## Verifying a change

"It loads without console errors" is not verification.
Drive the affected path in a browser and assert on the DOM.

- Matrix: default view is 46 rows x 10 columns; selecting all PDKs and all stages gives 20 columns and 920 cells.
- Filters: toggle each of the three axes, and clear all three to check the empty-state copy composes correctly.
- Cell page: 18 model rows plus one pinned baseline row, four populated charts, metric headers re-sort with a FLIP animation, raw/ratio toggle switches the values.
- Model page: architecture blocks, KPI count-ups, per-metric ranks, both breadcrumb links, Escape back to matrix.
- Both themes: check any colour you touched with a computed contrast ratio, not by eye.

After deploying, verify the live URL rather than trusting a green Actions run.

## Repo etiquette

Merging to `main` publishes to a public URL immediately, so `main` is the deploy trigger, not a staging area.
Land changes through a branch and PR.

Keep `README.md` accurate when behaviour changes.
It is the handoff document for the lab and it states specific counts that must stay true.
