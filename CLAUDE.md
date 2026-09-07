# CLAUDE.md

Guidance for Claude Code (or any agent) working in this repository.

## What this is

This repo (`HenryAI-stack/start`) is the source for the **cloudplay.at control-panel landing page** — a single static HTML page that lists Max's other self-built tools ("modules") as launchable tiles. It is deployed via GitHub Pages and served at the custom domain `cloudplay.at` (DNS via easyname, path-based redirects via redirect.pizza — see below).

The entire site is **one file**: `index.html`. There is no build step, no package manager, no framework, and no test suite. Everything (HTML, CSS, JS) lives inline in that single file — keep it that way unless explicitly asked to split it up.

## How it works

- On load, `loadModules()` calls the GitHub REST API (`/orgs/HenryAI-stack/repos`, falling back to `/users/HenryAI-stack/repos` if that 404s) to fetch all public, non-fork, non-archived repos.
- Repos listed in the `EXCLUDED_REPOS` array (top of the `<script>` block) are filtered out before rendering — this is how the landing page avoids showing a tile for itself, or for repos that aren't user-facing tools (config repos, workers, forks, experiments, etc.).
- Each remaining repo becomes a `.module` tile via `buildTile()`: name, GitHub description (or a placeholder if none is set), last-pushed timestamp (flagged `is-stale` if >30 days old), and a "LAUNCH" link to `https://cloudplay.at/<repo-name>`.
- Each tile tries to load the target site's own favicon from `https://henryai-stack.github.io/<repo>/favicon.ico`; on failure it falls back to a two-letter monogram derived from the repo name.
- Sorting (name A–Z/Z–A, most/least recently updated) and the dark/light theme toggle are both handled client-side and persisted in `localStorage` (`cloudplay-sort`, `cloudplay-theme`). No server-side state.
- Theming uses CSS custom properties on `:root`, overridden via `html[data-theme="light"]`. Fonts are IBM Plex Mono (code/labels) and IBM Plex Sans (body), loaded from Google Fonts.
- Accent colors cycle through the `ACCENTS` array (`#4CC9F0` cyan, `#F4A261` amber) per tile index.

## Maintenance gotchas

- **New tool repo added to `HenryAI-stack`?** It will automatically appear as a tile — no code change needed unless it should be *hidden* (add its name to `EXCLUDED_REPOS`) or given a description (set the repo description on GitHub; it's pulled live, not hardcoded here).
- **This repo's own name (`start`) is already in `EXCLUDED_REPOS`** so the landing page doesn't try to list itself. Don't remove it.
- The GitHub API call is unauthenticated client-side `fetch` — it's subject to GitHub's low unauthenticated rate limit (60 req/hr per IP). There's no caching/backoff; if this ever becomes a problem, that's the first place to look.
- `redirect.pizza`'s free tier doesn't support path wildcards, so `cloudplay.at/<repo>` routing for each tool is configured as an individual exact-match redirect rule per project — a newly added tool's tile will render and link correctly, but the `cloudplay.at/<repo>` redirect itself has to be added manually in redirect.pizza, outside this repo.
- No `CNAME` file lives in this repo; the custom domain is wired up entirely through external DNS/redirect config, not GitHub Pages' built-in custom-domain mechanism.

## Working in this repo

- Edit `index.html` directly. To preview, just open it in a browser (or serve the directory with any static file server) — the only external dependency at runtime is the GitHub API and Google Fonts, both fetched over the network.
- Keep additions consistent with the existing style: CSS custom properties for anything theme-dependent, `var(--mono)` for anything code/label-like, `escapeHtml()` around any GitHub-sourced text before inserting into the DOM.
- Commit messages in this repo's history are short, imperative, and describe the concrete change (e.g. "Add fetchRepos function to retrieve GitHub repos") — follow that convention.
