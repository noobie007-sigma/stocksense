# ENVIRONMENT.md — StockSense

**Status:** Finalized Day 3.

## 1. Environment Variables

**None.** This project has zero environment variables, `.env` files, or secrets of any kind.

Why: v1.0 has no backend, no database credentials, and no external API keys (per the Day 2 architecture decision to use a curated static JSON dataset instead of a live API). If a live API is ever added in a future version (see PRD Future Scope), this file should be updated at that time to document the required key(s) and how to obtain them.

## 2. Local Development Tools

| Tool | Version/Notes | Purpose |
|---|---|---|
| VS Code | Any recent version | Code editor |
| Live Server (VS Code extension) | By Ritwick Dey | Local static file server at `http://127.0.0.1:5500` with auto-refresh |
| Git | Any recent version | Version control |
| Browser (Chrome or Firefox) | Any recent version | Runtime + DevTools for debugging |

## 3. Configuration Files

| File | Purpose |
|---|---|
| `.gitignore` | Standard Node-template ignore file (created at repo setup); no build artifacts currently exist to ignore, but kept for future-proofing |
| `data/stocks.json` | Not a "config" file technically, but functions as the project's only data configuration — see `docs/SCHEMA.md` |

No `package.json`, no `webpack.config.js`, no `.env`, no `tsconfig.json` — none of these apply to a dependency-free static site.

## 4. Hosting Configuration (Preview — Finalized Day 9)

- **Host:** GitHub Pages
- **Source branch:** `main`, root folder
- **Custom domain:** None planned for v1.0
- **Build command:** None — GitHub Pages serves the static files directly

## 5. Browser Support Assumptions

Built and tested against current versions of Chrome and Firefox. No polyfills or transpilation are used, since we're targeting modern browsers only (a reasonable assumption for a personal capstone demo, not enterprise/legacy support).