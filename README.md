# Simulating a Smaller TAM Panel

**Live presentation:** https://robruu.github.io/tam-smaller-panel-slides/

Reveal.js slides exploring how much precision is lost when a TV audience
measurement (TAM) panel is reduced in size. The analysis:

- draws **10 independent samples of 1,000 individuals** from the full KSA TAM panel
- weights each sample to the national universe with raking (`anesrake`)
- compares audience estimates for broadcast spots against the full-panel TAM results
- uses bootstrap resampling to estimate margins of error for the smaller panel

## About this repository

This repository hosts only the **rendered presentation**, published to the
`gh-pages` branch with `quarto publish gh-pages`. The Quarto source and the
underlying panel data are not public.

Built with [Quarto](https://quarto.org) and [reveal.js](https://revealjs.com); analysis in R.
