# WAM Survey

A static academic research portal for **World Action Models (WAM)**.

## Pages

- `index.html`: home page and navigation
- `overview.html`: WAM concept and boundaries
- `roadmap.html`: development roadmap from world models to WAM
- `papers.html`: paper card database
- `data.html`: data-centric WAM survey
- `references.html`: references

## Update workflow

Most long-term updates should happen in:

- `data/papers.json`
- `data/datasets.json`
- `data/timeline.json`
- `data/references.json`

## Local preview

Do not open `index.html` directly if you want JSON cards to load. Use a local server:

```bash
python -m http.server 8000
```

Then open `http://localhost:8000`.

## GitHub Pages deployment

Create a public repository such as `wam-survey`, push these files to the repository root, then enable GitHub Pages from the `main` branch root.

Expected URL: `https://ffffurina.github.io/wam-survey/`
