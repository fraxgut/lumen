# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

LUMEN (Library of Universal Modular Elements for Neobrutalism) is a static
CSS/HTML component library with a neobrutalist design aesthetic. There is no
bundler, no Node.js tooling, and no JavaScript framework. The project is
licenced under the Licentia Publica Christiana (LPC) v1.0.

## Build and Development

There is no formal build system. The only compilation step is SCSS to CSS:

```sh
sass assets/css/main.scss assets/css/main.css
```

To preview components, serve the project root with any static HTTP server
(htmx requires HTTP to fetch component fragments):

```sh
python -m http.server 8000
```

The compiled `main.css` is committed to the repository. After editing any
`.scss` file, recompile and commit both the source and the compiled output.

## Architecture

### Component Loading

`index.html` is the single entry point. Each component is an HTML fragment
in `components/` loaded at runtime via htmx (`hx-get`, `hx-trigger="load"`).
Client-side interactivity (e.g. accordion toggling) uses _hyperscript
(`_=` attribute), not vanilla JavaScript.

### Styling Pipeline

- `assets/css/_variables.scss` — Design tokens: the "Frankifuscus" Base16
  colour palette and per-component variables (spacing, borders, shadows).
- `assets/css/components/_<name>.scss` — One SCSS partial per component.
- `assets/css/main.scss` — Aggregates all partials; also contains the
  base `html, body` and utility styles.

### Naming Conventions

CSS classes follow a BEM-like pattern:

- `.component` — block (e.g. `.button`, `.card`, `.alert`)
- `.component--modifier` — variant (e.g. `.button--strong`, `.card--yellow`)
- `.component__element` — child (e.g. `.card__title`, `.card__text`)

Every component supports at least two weight tiers: default (1px border)
and `--strong` (2px border with a solid box-shadow).

### External Dependencies (loaded via CDN)

- **htmx 2.0.4** — component fragment loading.
- **_hyperscript 0.9.13** — declarative client-side behaviour.
- **Boxicons 2.1.4** — icon set (classes prefixed `bx`/`bxs-`).

## Adding a New Component

1. Create `components/<name>.html` with the FALD header and markup.
2. Create `assets/css/components/_<name>.scss` with styles using variables.
3. Add any new design tokens to `assets/css/_variables.scss`.
4. Import the partial in `assets/css/main.scss`.
5. Add a `<section>` with `hx-get` in `index.html`.
6. Recompile CSS and commit all changed files.

## CI

A GitHub Actions workflow (`mirror-to-lokki.yml`) force-pushes `main` to
`lokkierp/lokki:lumen-upstream` on every push. No other CI checks exist.
