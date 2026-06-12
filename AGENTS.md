# Project: Simulating a Smaller TAM Panel

Saudi Arabia TV audience measurement (TAM) project testing whether a panel of
1,000 individuals can reproduce GRP results from the full TAM panel. The main
deliverable is a Quarto reveal.js presentation published at
https://robruu.github.io/tam-smaller-panel-slides/

## Key files

- `EstimateSmallerSample_improved.qmd` — the presentation source (the only
  document rendered by the Quarto project; `output-file: index`)
- `_quarto.yml` — minimal Quarto project: renders only the qmd above,
  `output-dir: _site`, `execute: freeze: auto`
- `3m3a-theme.scss` — custom reveal.js theme (dark `#0E2841` title slide)
- `.github/workflows/publish.yml` — CI publishing workflow
- Data inputs (NOT in git; the repo is public and uses a whitelist
  `.gitignore` that ignores everything except publishing essentials):
  - `demIndMay25_May31.rds` — individual demographics per day (week of
    25–31 May 2026); filter out `member_id == "zz"`
  - `viewingMay25_May31.rds` — viewing statements (channel, start/end time)
  - `HHsMay25_May31.rds` — household demographics
  - `KSA-TX4 Definition 20250626_External.xlsx` — TX4 demo position/value
    definitions (sheet `TX4-Demo-National`) used to decode `INDdemos` /
    `HHdemos` position strings
  - `RobertTables.xlsx` (sheet `Spots`) — spot list with TAM benchmark
    `GRPAbsolute`; Excel stores times on 1899-12-31, so re-attach the date
  - `tx4/*.tx4` — raw TX4 files, used to count TVs per household (rim 4)

## Analysis pipeline (in the qmd)

1. Decode demographics, join HH variables onto individuals, count TVs per HH
2. Build 5 rim variables (nationality, region, HH-size×nationality, TVs,
   gender×nationality); age deliberately excluded so all ages are sampled
3. Draw 10 samples of 1,000 individuals (frame: panel on 2026-05-25,
   `set.seed(42)`), rake each sample **per day** with `anesrake`, scale daily
   weights to the universe of 17,128,055
4. Recompute each spot's GRP per sample (channel + date + broadcast minute of
   spot start; broadcast day starts 03:00, overnight minutes wrap +1440) and
   compare against the TAM benchmark (`INDWgt` recalculation validates method)

## Publishing & freeze rule (IMPORTANT)

- Site is served from the `gh-pages` branch; every push to `master` triggers
  `.github/workflows/publish.yml`, which renders from the committed `_freeze/`
  cache and deploys `_site/` to `gh-pages` (peaceiris/actions-gh-pages).
- CI never runs the R pipeline and has no data files. **If the qmd's code
  changes, re-render locally (`quarto render`) and commit the updated
  `_freeze/` together with the qmd** — otherwise CI tries to execute R and
  fails (a deliberate guard against publishing stale results).
- Quarto quirks encoded in the workflow:
  - R + knitr/rmarkdown must be installed in CI even though freeze skips
    execution (Quarto checks for R on knitr documents).
  - `quarto publish`'s internal render bypasses the freeze cache; the
    workflow therefore runs `quarto render` explicitly and deploys `_site/`
    directly. CI Quarto is pinned to 1.9.32 (match local).
- Local Quarto: 1.9.32. Rendering from a valid freeze takes ~3 s; a full
  re-execution of the pipeline takes minutes (10 samples × 7 daily rakes).
