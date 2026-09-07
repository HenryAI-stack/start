# Notes

Running log of non-obvious maintenance work. Newest first.

## 2026-09-07 — Favicon rollout + CLAUDE.md

### Tile icons for every module

The landing page fetches each tile's icon from a fixed path,
`https://henryai-stack.github.io/<repo>/favicon.ico` (the repo's deployed
Pages root). None of the tool repos had a file there — several had one under
`src/` or `icons/`, or only an inline SVG/emoji in their own `<head>` — so
every tile fell back to the two-letter monogram.

Fixed by committing a `favicon.ico` to each tool's Pages root:

| Repo          | Icon                                   | Where it went          | Deploy path            |
| ------------- | -------------------------------------- | ---------------------- | ---------------------- |
| enginetime    | existing teal emblem (reused)          | repo root              | branch Pages           |
| vfr-tool      | existing triangle+plane (reused)       | repo root              | branch Pages           |
| shift-checker | amber/teal dots (from its `icon.svg`)  | repo root              | `static.yml` action    |
| people-os     | cyan compass (matches its 🧭)          | `public/favicon.ico`   | `deploy.yml` Vite build |
| recruit-os    | cyan bullseye (matches its 🎯)         | `public/favicon.ico`   | `deploy.yml` Vite build |

Key gotcha: the two Vite apps (`people-os`, `recruit-os`) can't take a file at
the repo root — Vite only publishes its build output, and it copies `public/`
verbatim to that output's root. So the icon must live in `public/favicon.ico`.

Verified live: all five `/<repo>/favicon.ico` URLs return `200 image/*`, and the
grid's logo `<img>` elements all report `naturalWidth > 0` with no monogram
fallback.

### This repo

- Added the standard `/init` header and a `## Commands` section to `CLAUDE.md`
  (no build/lint/test tooling; preview with a static server; deploy = push to
  `main`).
- `CLAUDE.md` "Maintenance gotchas" now documents the tile-icon favicon path
  and the `public/` requirement for the Vite projects.
- `index.html`: fixed a stray character in the `.logo-box` width declaration;
  added `<link rel="icon" href="favicon.ico">` + a `favicon.ico` (cyan play
  glyph) for this page's own tab; extended the `.module` reveal-stagger from 4
  to 6 `nth-of-type` rules to cover the current 5 tiles plus headroom.
