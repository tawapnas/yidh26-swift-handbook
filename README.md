# Swift Workshop Handbook

Participant handbook for the hackathon's Swift workshops, built with [MkDocs Material](https://squidfunk.github.io/mkdocs-material/).

## Run locally

```bash
pip install -r requirements.txt
mkdocs serve        # http://127.0.0.1:8000
```

## Publish to GitHub Pages

```bash
mkdocs gh-deploy
```

## Writing conventions

- Thai text, technical terms in English. Keep it short.
- Machine-specific remarks use custom admonitions:
  - `!!! mchip "เฉพาะ Mac M chip"` for Xcode 27 / Apple silicon only
  - `!!! intel "Intel Mac"` for Xcode 26 notes
- Code that must compile should live in the starter repos and be included with
  `--8<-- "path/to/File.swift:section"` (pymdownx.snippets, base path `../starter-repos`).
- Placeholders in `[brackets]` must be filled in before print.

## Print version

Use the browser's print to PDF (print styles hide navigation), or add a PDF plugin later.
