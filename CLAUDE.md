# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A Quarto Book for the university course **"Data Ethics and AI Law"** (BIP at THWS). It renders as a self-contained interactive HTML website deployed to GitHub Pages, with a parallel Typst/PDF path for a printable textbook. The target audience is interdisciplinary university students (CS, social work, business).

## Commands

```bash
# Local preview (live reload)
quarto preview

# Full render to _site/
quarto render

# Render a single chapter
quarto render legal-frameworks-ai.qmd --to moodle-html
```

The GitHub Actions workflow (`.github/workflows/`) auto-deploys on push to `main` using `quarto render --to moodle-html`.

## Architecture

### Output Format: `moodle-html`
The project uses a **custom Quarto extension** at `_extensions/c-kraus/moodle/`. This is the sole output format declared in `_quarto.yml`. The extension bundles:
- `thws.scss` — THWS university branding (orange `#ff6a00`, grey `#333333`, Roboto font)
- `interactions.js` — Client-side JS that transforms div containers into interactive elements at runtime
- `h4-fix.lua` — Pandoc Lua filter handling the H4-title convention and Typst/PDF rendering

### Interactive Elements (div containers)

All interactivity is driven by CSS class conventions processed by `interactions.js`. The pattern is always a fenced div with an `#### H4` as the title:

| Class | Result | Notes |
|---|---|---|
| `.flip-card` | Click-to-flip card | H4 = front label |
| `.case-study` + `.solution` sub-div | Textarea + reveal button | `.solution` is hidden until clicked |
| `.quick-check` | Multiple-choice quiz | `**bold**` list item = correct answer; `> blockquote` = explanation |
| `.drag-exercise` | Click-to-fill gaps | `*italic*` words become draggable tokens |
| `.details` | `<details>/<summary>` collapsible | H4 = summary label; becomes static box in PDF |

For PDF (Typst), the Lua filter converts `.flip-card` and `.details` to `#flashcard(title: [...])` blocks. The JS modules are skipped.

### Widgets

Standalone HTML/JS visualizations live in `widgets/` and are embedded via `<iframe>` directly in `.qmd` files:
```html
<iframe src="widgets/rejection.html" style="width: 100%; height: 930px; border: none;"></iframe>
```
Widgets must be fully self-contained (no external dependencies) since the book renders to a self-contained HTML file.

### Content Structure

- `index.qmd` — Preface / how-to-use guide
- `introbip.qmd` — Module 1: Coded Bias (ethics perspective)
- `legal-frameworks-ai.qmd` — Module 2: Legal Frameworks (GDPR, EU AI Act)
- `conclusion.qmd` — Synthesis
- `context/` — RAG source material (Markdown summaries of academic papers) used for content creation
