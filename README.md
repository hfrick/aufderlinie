[![Netlify Status](https://api.netlify.com/api/v1/badges/206bf17d-5fbd-404c-87df-71d208cd1107/deploy-status)](https://app.netlify.com/sites/hfrick/deploys)

# frick.ws

This repo is the source of <https://frick.ws>.

## Workflow

The website is build with [hugodown](https://github.com/r-lib/hugodown). 

- Posts are turned from `.Rmd` to `.md` by knitting them. They need `output: hugodown::hugo_document` in the yaml up top.
- Hugo then turns the `.md` to `.html` - see it happen with `hugodown::hugo_start()`.
