[![Netlify Status](https://api.netlify.com/api/v1/badges/206bf17d-5fbd-404c-87df-71d208cd1107/deploy-status)](https://app.netlify.com/sites/hfrick/deploys)

# frick.ws

This repo is the source of <https://www.frick.ws>.

This is a Quarto website deployed to Netlify.

- `_quarto.yml` defines it as a Quarto website project, served at https://www.frick.ws.
- `netlify.toml` uses the `@quarto/netlify-plugin-quarto` plugin, which means Netlify builds the site by running Quarto as part of its build pipeline.
- `_publish.yml` ties the project to a specific Netlify site (ID 206bf17d-...), used by `quarto publish netlify`.
- `execute: freeze: auto` in the Quarto config means computations are frozen locally — Netlify doesn't need R/Python to build, it just renders the pre-frozen markdown to HTML.

So the workflow is: 

- run computations locally (results get frozen, i.e. go into `_freeze`)
- push to the repo
- Netlify picks up the changes and builds/deploys the site
