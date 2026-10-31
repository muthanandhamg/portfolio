# portfolio

A single-page DevOps capability portfolio for Muthanandham Gnanamuthuraj — a product
landing page for what the author can build, from code to a running cloud deployment.

**Live:** <https://muthanandhamg.github.io/portfolio/>

## Run

The whole site is one file, `index.html`, with no build step and no dependencies beyond
two Google Fonts. Open it in a browser, or serve it locally:

```bash
python3 -m http.server 8000   # then open http://localhost:8000
```

## Deploy

GitHub Pages serves `index.html` from the root of `main`, so a push to `main` is a deploy.

## Project docs

This repository holds code only. Design, specs, decisions, plans, agent rules and project memory live in the maintainer's private workspace (personal-ai-os) under `contexts/portfolio/`. Its `repo-docs/` folder keeps the original paths, so a code comment citing a path such as `design/...` or `docs/...` refers to `contexts/portfolio/repo-docs/<that path>`.
