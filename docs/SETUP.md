# SETUP.md — StockSense

**Status:** Finalized Day 3. Follow this to get the project running on any machine from scratch.

---

## 1. Prerequisites

| Tool | Needed? | Why |
|---|---|---|
| A code editor (VS Code recommended) | Yes | To view/edit `index.html`, `style.css`, `script.js` |
| VS Code "Live Server" extension (by Ritwick Dey) | Yes | Serves the site over `http://localhost` so `fetch()` calls work (browsers block `fetch()` on `file://` pages) |
| Git | Yes | Version control, pushing to GitHub |
| A modern browser (Chrome or Firefox) with DevTools | Yes | Testing and debugging |
| Node.js / npm | **No** | No dependencies, no build step — vanilla HTML/CSS/JS by design |
| Any framework CLI (React, Vue, etc.) | **No** | Not used — see `docs/ARCHITECTURE.md` |
| Database software | **No** | Data is a static JSON file, not a database |
| API keys / environment variables | **No** | No external APIs are called at runtime |

---

## 2. First-Time Setup

1. Clone the repo:
   ```
   git clone https://github.com/<your-username>/stocksense.git
   cd stocksense
   ```
2. Open the folder in VS Code:
   ```
   code .
   ```
3. Install the **Live Server** extension (Extensions panel → search "Live Server" → Install).
4. Right-click `index.html` in the file explorer → **"Open with Live Server"**.
5. Your browser opens automatically to `http://127.0.0.1:5500/index.html` (port may vary).

That's the entire setup. No `npm install`, no build command, no `.env` file.

---

## 3. Running the Project Day-to-Day

- Every time you open the project, just right-click `index.html` → **"Open with Live Server"**.
- Live Server auto-refreshes the browser every time you save a file — no manual reload needed.
- To stop the server: click **"Port: 5500"** in the VS Code status bar (bottom right) and select **"Close Server"**.

---

## 4. Verifying Everything Works

1. Search `TCS` → should display the Tata Consultancy Services record.
2. Search `infosys` (lowercase, partial name) → should also match, via `searchAliases`.
3. Search `xyz123` → should show "Couldn't find that stock..." with no console errors.
4. Open DevTools (**F12**) → **Console** tab → confirm no red error messages on load or search.

---

## 5. Deploying (Preview — Full Steps on Day 9)

This project deploys as a static site to **GitHub Pages** — no build step required. Full deployment steps are documented in the Implementation Blueprint's Day 9 section and will be executed then, not today.