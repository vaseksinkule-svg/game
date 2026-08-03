# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

A static, single-page portfolio website ("Moje Aplikace") showcasing six application cards. There is no build system, package manager, framework, linter, or test suite — just plain HTML and CSS.

- `index.html` — the entire page: a hero header, six `.app-card` sections, and a footer
- `style.css` — all styling

## Development

No build or install step. To preview changes, open `index.html` directly in a browser or serve the directory:

```bash
python3 -m http.server 8000
```

## Conventions

- **Language:** All user-facing content is in Czech (`<html lang="cs">`). Keep new copy in Czech.
- **App cards:** Each project is a `<section class="app-card" id="appN">` containing an `.app-image`, and an `.app-info` block with an `.app-tag` category badge, heading, description, `.tech-stack` list, and a `.btn` link. Alternate cards add the `reverse` class to flip the image/text layout (implemented via `direction: rtl` in CSS, with a single-column fallback under 700px).
- **Styling:** Dark theme driven by CSS custom properties on `:root` (`--bg`, `--surface`, `--accent`, `--accent2`, `--text`, `--muted`, `--radius`). Use these variables rather than hard-coded colors. Fluid typography uses `clamp()`.
- **Images:** Card images are currently placehold.co placeholders sized 600×400.
- **Placeholder content:** Card titles ("Název Aplikace N"), descriptions (lorem ipsum), links (`href="#"`), and the footer email are placeholders awaiting real project data.
