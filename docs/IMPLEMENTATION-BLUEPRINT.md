# Implementation Blueprint — "StockSense" (working title)
### AB Talks 60-Day Claude AI Challenge — 10-Day Capstone
**Days 2–10 | Single Source of Truth for the Remainder of the Build**

---

## 0. Project Foundation (Do Not Re-Decide This)

> Read this section first, every day. It is fixed context carried over from Day 1 discovery. Nothing here should be re-argued or redesigned in later days — only built on.

- **Project:** A web app that lets a financially-untrained beginner search any Indian (NSE/BSE) stock and instantly see a clean, single-screen breakdown of whether it looks like a healthy investment — no jargon, no digging through multiple pages.
- **Primary user:** The builder themself — curious, intelligent, but with zero formal finance background. Comfortable with JavaScript, Python, Java, HTML/CSS at a "some experience" level (tutorials/small projects, not professional).
- **Time budget:** ~1–2 hours/day, 9 remaining days.
- **Platform:** Web app, deployed as a static site on **GitHub Pages** (no backend server in v1.0 — all data fetching happens client-side, directly from the browser).
- **Core design principle:** One dashboard. One search. All decision-critical info visible without navigating away. This principle overrides any temptation to add extra pages, tabs, or flows.
- **Data scope:** Indian stock market (NSE/BSE) only. **[Updated Day 2]** Research confirmed NSE/BSE have no official public API, and free third-party wrappers either require server-side keys (unsafe client-side) or a self-hosted proxy (adds backend complexity we don't need). Decision: **a curated static JSON dataset (~15–20 well-known NSE stocks) is the primary v1.0 data source**, not a fallback. This removes the project's single biggest risk entirely, keeps the architecture pure-static, and guarantees demo reliability. Every record carries a `lastUpdated` date, shown in the UI, so the tool is honest about not being real-time. "Connect a live data API" is now explicit future scope.

### v1.0 Feature Scope (locked from Day 1)

**Core (must ship):**
1. Company snapshot — price, market cap, sector, short description
2. Key financial ratios explained in plain English, each with a simple "good / average / concerning" indicator
3. Red flags — a short, clearly-labeled list of things to watch out for

**Stretch (only if time allows, in this priority order):**
4. Recent price chart/trend
5. Plain-English company summary ("mini research report" style)
6. Comparison against industry average

**Explicitly out of scope for v1.0:** user accounts/login, saved watchlists, portfolio tracking, real-time streaming prices, buy/sell execution, notifications, mobile app (native), multi-language support, any paid API tier.

### Tech Stack — FINALIZED Day 2
- **Frontend:** Plain HTML5, CSS3, vanilla JavaScript (ES6+). No framework, no build step.
- **Backend:** None.
- **Data source:** `data/stocks.json` — a curated static dataset bundled with the site (see SCHEMA.md). No live API, no keys, no CORS.
- **Hosting:** GitHub Pages.
- **Auth:** None (no accounts in v1.0).
- **AI/API calls at runtime:** None — ratio assessment and red flags are rule-based logic in `script.js`, not AI-generated.
- **Stretch-only tool:** Chart.js via CDN, only if the Day 7 price-chart stretch goal is attempted.
- Full rationale in `docs/ARCHITECTURE.md`. This is now locked and should not be revisited without a strong reason.

---

## Day 2 — Requirements Validation + Technical Design — ✅ COMPLETED

### 🎯 Objective (as executed)
De-risk the single biggest unknown (Indian stock data access) and lock the full technical design — stack, architecture, schema, API contract, wireframes, and folder structure — so every remaining day is pure execution, not decision-making.

### What actually happened
Research confirmed NSE/BSE have no official public API. Free third-party options require either exposing an API key client-side (unsafe) or self-hosting a proxy server (adds backend complexity). **Decision: ship v1.0 with a curated static JSON dataset (~15–20 NSE stocks) as the primary data source, not a fallback.** This was a deliberate deviation from the original Day 2 plan (which assumed live-API testing) — approved because it removes the biggest project risk, keeps the architecture genuinely simple, and protects demo-day reliability given the 1–2 hr/day budget.

### ✅ Completed today
- [x] GitHub repo created and cloned locally (`stocksense`)
- [x] Initial folder structure created and pushed
- [x] Tech stack finalized (see Section 0 above): vanilla HTML/CSS/JS, static JSON data, GitHub Pages, no backend, no auth, no AI at runtime
- [x] Full system architecture designed (`docs/ARCHITECTURE.md`) — component diagram, data flow, request lifecycle
- [x] Data schema designed and validated against every PRD user story (`docs/SCHEMA.md`)
- [x] Internal data-access interface designed (`docs/API.md`) — `loadStockDataset()`, `searchStock()`, `getRatioAssessment()`, `getRedFlags()`, formatting helpers
- [x] Full UI wireframes for Idle / Loading / Results / Error states, desktop and mobile (`docs/UI-WIREFRAMES.md`)
- [x] Complete folder/file structure finalized (`docs/PROJECT-STRUCTURE.md`)

### 📂 Files and folders now in the repo
```
stocksense/
├── index.html          (empty skeleton — no code yet)
├── style.css            (empty skeleton — no code yet)
├── script.js            (empty skeleton — no code yet)
├── data/                (empty — populated Day 3)
├── assets/              (empty)
├── docs/
│   ├── ARCHITECTURE.md
│   ├── SCHEMA.md
│   ├── API.md
│   ├── UI-WIREFRAMES.md
│   ├── PROJECT-STRUCTURE.md
│   └── Implementation_Blueprint_Day2-10.md
├── README.md
└── .gitignore
```

### ➡️ Handoff notes for Day 3
Everything needed to start writing real code exists: the exact JSON schema (`docs/SCHEMA.md`), the exact function contracts (`docs/API.md`), and the exact wireframes (`docs/UI-WIREFRAMES.md`). **Day 3 no longer needs to test any live API or handle CORS** — it now focuses on (1) compiling the first batch of real stock data into `data/stocks.json` per the finalized schema, and (2) writing the first working `fetch()` + `searchStock()` pipeline against that local file. This is simpler and lower-risk than the original Day 3 plan.

---

## Day 2 Readiness Check (for Day 3)

- **Timeline realistic?** Yes — removing the live-API risk *reduces* the work remaining. Days 4–6 (core features) are unaffected by today's change, since they were always designed to work off a confirmed data shape, not a specific API.
- **Scope creep?** None introduced. If anything, scope is now safer: no proxy server, no key management, no rate-limit handling.
- **Can Day 3 start immediately with no further planning?** Yes — `docs/SCHEMA.md` and `docs/API.md` fully specify what Day 3 needs to build.
- **Recommendation:** No simplification needed beyond what's already been decided today. Proceed as planned.

---

## Day 3 — Environment Setup + Curated Dataset + First End-to-End Data Flow — ✅ COMPLETED

### What actually happened
Live Server was installed and confirmed working. 6 real, schema-valid stock records were compiled into `data/stocks.json` (TCS, INFY, RELIANCE, HDFCBANK, ITC, ZOMATO — more can be added incrementally as needed during Days 4–6, since 6 is enough to build and test all v1.0 UI logic against). `loadStockDataset()` and `searchStock()` were implemented exactly per `docs/API.md`. End-to-end flow verified: valid ticker search, partial/alias name search, invalid search, and empty query all behave correctly with no console errors. Full detail in `docs/DAY3-SUMMARY.md`.

### ✅ Completed today
- [x] Local dev environment (Live Server) installed and verified
- [x] `data/stocks.json` populated with 6 schema-valid records
- [x] `loadStockDataset()` and `searchStock()` implemented and working
- [x] `index.html` built with real search bar + results container
- [x] `style.css` given minimal functional styling
- [x] End-to-end tested: valid search, alias search, invalid search, empty query
- [x] Trunk-based branching strategy confirmed (solo project, direct-to-`main` commits)
- [x] Code committed and pushed

### ➡️ Handoff notes for Day 4
The data pipeline is fully working and schema-locked. Day 4 can go straight into building the real, styled **Company Snapshot card**, replacing today's raw-JSON display — no setup, environment, or architecture work remains. Next functions to implement: `formatCurrency()` / `formatLargeNumber()` per `docs/API.md`.

### 🎯 Objective
Compile the first real dataset per SCHEMA.md, and get a fully working (if ugly) end-to-end flow: type a ticker → look it up in local JSON → display raw data on screen. This proves the whole pipeline works before any design polish. **No live API, no CORS, no keys — this is simpler than originally planned.**

### 📖 What I'll learn
- Setting up a local dev environment for a static site (VS Code + Live Server extension, or `python -m http.server`)
- Structuring and hand-compiling a clean JSON dataset from public sources
- Basic `fetch()` + `async/await` patterns in JavaScript, applied to a local file
- Rendering fetched data into the DOM

### 🛠 Features to build
- `data/stocks.json` populated with 15–20 real companies, matching SCHEMA.md exactly
- A working search input + button
- Raw data display (unstyled) confirming the full pipeline works via `searchStock()`

### 📝 Step-by-step implementation plan
1. Set up local preview (Live Server extension in VS Code, or equivalent) so you can see changes instantly.
2. Compile `data/stocks.json`: pick 15–20 well-known, well-covered NSE stocks across a few sectors (e.g., TCS, Infosys, Reliance, HDFC Bank, ITC, Maruti Suzuki, Sun Pharma, Bajaj Finance...). For each, fill in every field from SCHEMA.md using publicly available figures (e.g., from screener.in, moneycontrol, or company investor-relations pages) — use `null` for anything you can't confidently source, never a guessed number.
3. In `index.html`, add the search input and "Search" button per `docs/UI-WIREFRAMES.md`'s Idle state.
4. In `script.js`, implement `loadStockDataset()` and `searchStock(query)` exactly per `docs/API.md`'s contracts.
5. Wire the button's click event to call `searchStock()` and `console.log()` the result first (verify in browser dev tools before touching the DOM).
6. Once confirmed in console, render the raw matched object into a `<div id="results">` — no styling yet, just proving data flows from input → local lookup → screen.
7. Commit and push to GitHub.

### 📂 Files and folders to create or modify
- `data/stocks.json` (populate per SCHEMA.md)
- `index.html` (search input, button, results container)
- `script.js` (`loadStockDataset()`, `searchStock()`, basic render function)
- `style.css` (leave minimal/empty — styling comes later)

### 🔗 APIs, libraries, services, or tools to integrate
- None external — local JSON file only
- VS Code Live Server (or `python -m http.server`) for local preview only — not part of the shipped product

### 🧪 Testing tasks
- Test with at least 3 different valid tickers/company names from your dataset
- Test with a misspelled/invalid ticker and confirm `searchStock()` returns `null` gracefully (no uncaught error)
- Test an empty search submission per API.md's empty-query handling

### 🐞 Common issues and debugging tips
- **"Cannot read property of undefined"**: double-check `data/stocks.json` matches SCHEMA.md exactly (e.g., `ratios` object always present, even if fields inside are `null`).
- **Nothing happens on button click**: check that the event listener is attached after the DOM is loaded (`DOMContentLoaded`, or place the script tag at the end of `<body>`).
- **`fetch()` fails locally**: some browsers block `fetch()` of local files opened via `file://` — this is exactly why Live Server (which serves over `http://localhost`) is required, not optional.
- **Page looks blank**: check browser console for errors first, always, before changing code.

### ✅ End-of-day checklist
- [ ] `data/stocks.json` populated with 15–20 real, schema-valid stock records
- [ ] Local dev environment runs and auto-refreshes on save
- [ ] Typing a valid ticker/name and clicking search displays the matched record on screen
- [ ] Invalid ticker and empty query both handled gracefully per API.md
- [ ] Code committed and pushed to GitHub

### 📸 Expected project state and screenshots to capture
- Screenshot of `data/stocks.json` open in the editor showing 2–3 complete records
- Screenshot of the page showing a raw matched record displayed after a successful search

### ➡️ Handoff notes for Day 4
Confirm: the dataset is populated and the `searchStock()` pipeline works end-to-end. Day 4 will build the real "Company Snapshot" card using this confirmed, schema-locked data shape — no guessing at field names, since SCHEMA.md and API.md are both already finalized.

---

## Day 4 — Core Feature 1: Company Snapshot UI — ✅ COMPLETED

### What actually happened
Built the styled Snapshot card (company name, ticker, sector, price, market cap, description, last-updated date), plus `formatCurrency()`, `formatLargeNumber()`, `formatDate()`, and an `escapeHtml()` safety helper. Verified across multiple stocks including one with a `null` ratio field (doesn't affect Snapshot, confirmed no crash). Reserved empty `#ratios-section`/`#red-flags-section` containers in the DOM for Days 5–6. Full detail in `docs/DAY4-SUMMARY.md`.

### ✅ Completed today
- [x] Snapshot card renders cleanly for multiple real stocks
- [x] Loading and error states both work
- [x] Layout holds up on a resized/narrow browser window
- [x] Code committed and pushed

### ➡️ Handoff notes for Day 5
Snapshot card is done and reusable patterns exist (formatting helpers, loading/error states, `escapeHtml`). `#ratios-section` container already exists in `index.html`. Day 5 builds the Key Ratios section directly into it, reusing the same card/section styling conventions.

### 🎯 Objective
Turn the raw data dump into the first real, polished feature: a clean company snapshot card — the top section of the single-screen dashboard.

### 📖 What I'll learn
- Structuring readable, semantic HTML for a card-based UI
- Basic responsive CSS (flexbox/grid) for a dashboard layout
- Defensive coding: handling missing/null fields gracefully

### 🛠 Features to build
- Company Snapshot card: company name, ticker, current price, market cap, sector, short description

### 📝 Step-by-step implementation plan
1. Design the snapshot card's HTML structure inside the `#results` container (replace raw JSON dump from Day 3).
2. Map the confirmed data fields (from Day 3 handoff) into the card: name, ticker, price, market cap, sector, description.
3. Add a `formatCurrency()` and `formatLargeNumber()` helper (e.g., turn `2847300000000` into `₹28.47 Lakh Cr` or similar Indian numbering convention) — this matters a lot for a beginner-friendly feel.
4. Style the card with CSS: clear visual hierarchy (big price, clear labels), using flexbox/grid for layout.
5. Add a loading state (e.g., "Fetching data...") shown while the fetch is in progress, and an error state ("Couldn't find that stock — check the ticker and try again") for failed searches.
6. Test on both desktop-width and narrow/mobile-width browser windows; adjust CSS for responsiveness.

### 📂 Files and folders to create or modify
- `index.html` (snapshot card markup)
- `style.css` (card styling, responsive layout, loading/error states)
- `script.js` (render function for snapshot card, formatting helpers, loading/error state logic)

### 🔗 APIs, libraries, services, or tools to integrate
- None new — building on Day 2/3's data source

### 🧪 Testing tasks
- Test snapshot rendering with 5+ different real stocks across different sectors
- Test loading state is visible (even briefly) and error state triggers correctly on bad input
- Resize browser window to confirm layout doesn't break

### 🐞 Common issues and debugging tips
- **Numbers look wrong/too long**: Indian large-number formatting (Lakh/Crore) differs from Western (million/billion) — decide explicitly which convention to use and apply it consistently.
- **Layout breaks on narrow screens**: use `flex-wrap: wrap` or CSS Grid with `minmax()` rather than fixed pixel widths.
- **Missing fields for some stocks**: wrap each field render in a check (e.g., `field || "N/A"`) so one missing field doesn't break the whole card.

### ✅ End-of-day checklist
- [ ] Snapshot card displays cleanly for multiple real stocks
- [ ] Loading and error states both work
- [ ] Layout holds up on a resized/narrow browser window
- [ ] Code committed and pushed

### 📸 Expected project state and screenshots to capture
- Screenshot of a fully rendered snapshot card for a real stock (e.g., search "TCS" or "INFY")
- Screenshot of the error state for an invalid search

### ➡️ Handoff notes for Day 5
Snapshot card is done and reusable patterns exist (formatting helpers, loading/error states). Day 5 builds directly below the snapshot card: the Key Ratios section, reusing the same card/section styling conventions established today for visual consistency.

---

## Day 5 — Core Feature 2: Key Ratios, Explained in Plain English — ✅ COMPLETED

### What actually happened
Built `getRatioAssessment()` and `RATIO_CONFIG` covering P/E, EPS, Dividend Yield, and Debt-to-Equity — each with formatted value, plain-English explanation, and a Good/Average/Concerning badge. `null` fields handled explicitly (never NaN, never a false zero). Verified across TCS (complete data), HDFC Bank and Zomato (each missing one ratio field). Snapshot card confirmed unaffected. Full detail in `docs/DAY5-SUMMARY.md`.

### ✅ Completed today
- [x] Ratios section renders below the snapshot card with consistent styling
- [x] Each ratio has a value, explanation, and color-coded indicator
- [x] Disclaimer text is present
- [x] Tested against 3 stocks with varying/missing data
- [x] Code committed and pushed

### ➡️ Handoff notes for Day 6
Ratios section complete with reusable card/row pattern. `getRatioAssessment()`'s output ratio values can feed directly into Day 6's red-flag rules. Day 6 builds the Red Flags card directly below, then does a full-page cohesion pass.

### 🎯 Objective
Build the heart of the product: a ratios section that doesn't just show numbers, but explains what each one means and whether it looks good, average, or concerning — in plain English.

### 📖 What I'll learn
- Translating financial logic into simple conditional rules (thresholds for "good/average/concerning")
- Writing beginner-friendly explanatory copy alongside data
- Building reusable UI components (a "ratio row" pattern repeated for each metric)

### 🛠 Features to build
- Key Ratios section with at minimum: P/E ratio, EPS, Dividend Yield, Debt-to-Equity (adjust based on what the data source actually provides — confirm field availability first)
- Each ratio shows: the number, a one-line plain-English explanation of what it means, and a color-coded indicator (green/yellow/red or "Good/Average/Concerning" label)

### 📝 Step-by-step implementation plan
1. Confirm exactly which ratio fields are available from your data source (some sources provide P/E and EPS but not debt ratios — adapt the list to reality, don't force fields that don't exist).
2. For each ratio, write a simple plain-English one-liner (e.g., "P/E ratio: how much investors are paying for each ₹1 of profit. Lower can mean better value, but very low can also mean the market has doubts.").
3. Define simple, defensible threshold rules for the good/average/concerning indicator per ratio (e.g., P/E under 15 = "Good value," 15–30 = "Average," above 30 = "Priced high" — these are simplifications for a beginner tool, not professional financial advice, and should be labeled as such).
4. Build a reusable HTML/CSS "ratio row" component: label, value, explanation, colored indicator badge.
5. Loop through your ratio list in JS and render each row using this component.
6. Add a small disclaimer near this section: "These are simplified educational guidelines, not financial advice."

### 📂 Files and folders to create or modify
- `index.html` (ratios section container)
- `style.css` (ratio row styling, color-coded badges)
- `script.js` (ratio threshold logic, explanatory text mapping, render loop)

### 🔗 APIs, libraries, services, or tools to integrate
- None new — same data source, different fields

### 🧪 Testing tasks
- Test across stocks with clearly different ratio profiles (e.g., a high-P/E tech stock vs. a low-P/E established company) to confirm the good/average/concerning logic feels reasonable, not arbitrary
- Test a stock missing one or more ratio fields — confirm it doesn't break the section

### 🐞 Common issues and debugging tips
- **Threshold logic feels wrong for certain sectors**: this is a known simplification — note it in the disclaimer rather than trying to build sector-specific logic in v1.0 (that's future scope).
- **A ratio value is `null` or `NaN`**: display "Data not available" for that row instead of "NaN" or breaking layout.

### ✅ End-of-day checklist
- [ ] Ratios section renders below the snapshot card with consistent styling
- [ ] Each ratio has a value, explanation, and color-coded indicator
- [ ] Disclaimer text is present
- [ ] Tested against at least 3 stocks with varying financial profiles
- [ ] Code committed and pushed

### 📸 Expected project state and screenshots to capture
- Screenshot of the full page so far: snapshot card + ratios section together for one real stock

### ➡️ Handoff notes for Day 6
Ratios section complete with reusable "row" component pattern. Day 6 builds the Red Flags section directly below, and should reuse the same visual language (badges, color-coding) established in Days 4–5 for a cohesive single-screen dashboard.

---

## Day 6 — Core Feature 3: Red Flags + Visual Cohesion Pass — ✅ COMPLETED (+ Deployment pulled forward from Day 9)

### What actually happened
Built `getRedFlags()` (high P/E, high Debt-to-Equity, zero dividend yield, low EPS rules — all null-safe) and the Red Flags card (amber warning / green all-clear states). Added the required footer. Did a spacing/cohesion pass across all three cards. **v1.0 core feature set is complete.** Additionally, per this session's explicit requirement for a live demo by end of Day 6, deployment to GitHub Pages was completed today instead of Day 9 — verified working with zero console errors on the live URL. Full detail in `docs/DAY6-SUMMARY.md`.

### ✅ Completed today
- [x] Red Flags section works for both "has flags" and "no flags" cases
- [x] Full-page visual cohesion pass completed
- [x] Footer added and visible in all states, confirmed on live deployed site
- [x] **Deployed to GitHub Pages today (moved up from Day 9)** — live and verified
- [x] Core v1.0 feature set (Snapshot + Ratios + Red Flags) functionally and visually complete
- [x] Code committed and pushed

### ➡️ Handoff notes for Day 7
All three core features are live and working. Because deployment already happened today, **Day 9 no longer needs first-time deployment** — it will instead do deeper regression testing of the live site (see Day 9 section, updated below). Day 7 is a decision point exactly as originally planned: attempt stretch goals if on schedule, or use the day as a polish buffer if not. Given deployment is done early, there is likely extra runway — lean toward attempting stretch goal #1 (price chart) unless testing reveals issues first.

### 🎯 Objective
Complete the three core v1.0 features by adding the Red Flags section, then do a first cohesion pass so the whole single-screen dashboard feels like one designed product, not three separate sections bolted together.

### 📖 What I'll learn
- Writing simple rule-based "flag" logic from existing data (no new API calls needed — reuse what you already fetched)
- Visual hierarchy and whitespace principles for dashboard-style UIs
- Doing a lightweight design/consistency audit of your own work

### 🛠 Features to build
- Red Flags section: a short list (2–5 items) of specific concerns detected from the stock's data (e.g., "High P/E relative to typical range," "Low or no dividend," "Debt levels appear high") — or a friendly "No major red flags detected" state if none trigger
- Visual cohesion pass across the full page

### 📝 Step-by-step implementation plan
1. Define red-flag rules using data you already have (reuse ratio values from Day 5 — e.g., if Debt-to-Equity > some threshold, flag it; if P/E is extremely high with no clear justification, flag it).
2. Write each flag as a short, specific, plain-English sentence — avoid vague alarmism ("this might be risky" is weak; "Debt-to-Equity is notably higher than typical for this sector" is specific).
3. Build the Red Flags UI: a distinct visually-flagged section (e.g., warm-colored left accent or icon, NOT a full-width stripe — see design guidance to avoid AI-generated-looking accent bars) with a bulleted list.
4. Handle the "no flags" case with a positive, clear message — don't leave the section awkwardly empty.
5. **Cohesion pass**: view the full page top to bottom. Check spacing consistency between sections, consistent font sizes for labels vs. values vs. explanations, consistent color palette across snapshot/ratios/flags, and consistent card/section styling.
6. Fix any visual inconsistencies found during the cohesion pass.

### 📂 Files and folders to create or modify
- `index.html` (red flags section)
- `style.css` (flag styling + any cohesion fixes across existing sections)
- `script.js` (flag-detection logic, render function)

### 🔗 APIs, libraries, services, or tools to integrate
- None new

### 🧪 Testing tasks
- Test with a stock likely to trigger multiple flags and one likely to trigger none — confirm both states look intentional and polished
- Full visual review at both desktop and narrow-browser widths

### 🐞 Common issues and debugging tips
- **Flags feel arbitrary or scary**: reground each flag in the specific number that triggered it (show the value alongside the flag text) so it reads as informative, not alarming.
- **Page feels visually disjointed**: usually caused by inconsistent spacing units — pick one spacing scale (e.g., multiples of 8px) and apply it everywhere.

### ✅ End-of-day checklist
- [ ] Red Flags section works for both "has flags" and "no flags" cases
- [ ] Full-page visual cohesion pass completed — consistent spacing, color, typography across all 3 core sections
- [ ] Core v1.0 feature set (Snapshot + Ratios + Red Flags) is now functionally and visually complete
- [ ] Code committed and pushed

### 📸 Expected project state and screenshots to capture
- Full-page screenshot of the complete core dashboard (all 3 sections) for one real stock — this is effectively your "v1.0 core" milestone screenshot

### ➡️ Handoff notes for Day 7
All three core (must-have) features are done. State clearly at the start of Day 7 how much time/energy is left relative to the 9-day budget. Day 7 is a decision point: attempt stretch goals (price chart, plain-English summary, industry comparison — in that priority order) only if Days 2–6 stayed on schedule. If behind schedule, Day 7 becomes a polish-and-buffer day instead — this is an acceptable and pre-approved outcome, not a failure.

---

## Day 7 — Stretch Features (If On Schedule) or Polish Buffer (If Behind)

### 🎯 Objective
Either extend the product with one or more pre-approved stretch features, or use this day as a buffer to solidify and polish the core if earlier days ran long. **Decide which mode at the very start of the day** based on honest progress so far — don't let ambition override the schedule.

### 📖 What I'll learn
- (If stretch) Basic charting with a lightweight library, or additional data-shaping techniques
- (If buffer) The discipline of polishing over adding — a real product skill

### 🛠 Features to build
**If ahead of/on schedule**, attempt stretch features in this order (stop after any one if time runs low):
1. Recent price chart/trend (a simple line chart using a lightweight library like Chart.js, if historical price data is available from your source; otherwise a simplified sparkline from whatever recent data points you can get)
2. Plain-English company summary (a short auto-generated paragraph combining snapshot + ratio data into a narrative, e.g., "TCS is a large IT services company. It's trading at a P/E of X, which is [assessment]...")
3. Industry/sector average comparison (only if your data source provides sector-level aggregate data — if not, this is descoped, not forced)

**If behind schedule**, instead:
- Fix any bugs found so far
- Improve mobile responsiveness
- Improve copy/wording clarity in ratio explanations and flags
- Add a simple "how to read this page" tooltip or intro line for first-time users

### 📝 Step-by-step implementation plan
1. **Start of day: honest checkpoint.** Compare actual progress to the Day 2–6 plan. Choose stretch-mode or buffer-mode.
2. If stretch-mode: implement chart.js (or chosen library) via CDN script tag (no npm/build step needed for a static GitHub Pages site), feed it historical price data if available, style to match the dashboard.
3. If buffer-mode: work through a self-made bug/polish list, starting with anything that would embarrass you in a live demo.
4. Either way, end the day with a full-page test across at least 5 different real stocks to catch edge cases.

### 📂 Files and folders to create or modify
- `index.html`, `style.css`, `script.js` (extend or refine, depending on mode)
- Add a `<script src="chart.js CDN link">` if implementing the price chart

### 🔗 APIs, libraries, services, or tools to integrate
- Chart.js via CDN (only if attempting the price chart stretch goal)

### 🧪 Testing tasks
- Test any new stretch feature across multiple stocks, including ones with incomplete data
- Full regression test of core features to ensure nothing broke while adding/polishing

### 🐞 Common issues and debugging tips
- **Chart.js not rendering**: check the canvas element exists in HTML before the script runs, and that data is in the array format Chart.js expects.
- **Scope creep temptation**: if a stretch feature is taking more than ~40% of today's time budget with no clear finish line in sight, stop and fall back to polish-mode — an unfinished stretch feature looks worse in a demo than a polished core.

### ✅ End-of-day checklist
- [ ] Honest start-of-day checkpoint was done and mode was chosen deliberately
- [ ] Either: one stretch feature is fully working, OR core product is measurably more polished/bug-free
- [ ] Full regression test passed across 5+ stocks
- [ ] Code committed and pushed

### 📸 Expected project state and screenshots to capture
- Screenshot showing whichever was achieved: new stretch feature in action, or before/after polish comparison

### ➡️ Handoff notes for Day 8
State clearly what was added/changed today, and confirm the app is feature-complete for v1.0 (core, plus whatever stretch made it in). Day 8 is dedicated entirely to testing and bug-fixing — no new features should be proposed or built on Day 8.

---

## Day 8 — Testing & Bug Bash

### 🎯 Objective
Systematically test the entire app as if you were a real first-time user, and fix every bug found. No new features today — this is quality-hardening before deployment.

### 📖 What I'll learn
- Manual test-case design (thinking through edge cases deliberately, not randomly)
- Cross-browser/responsive testing basics
- Prioritizing bugs (must-fix vs. nice-to-fix) under a time constraint

### 🛠 Features to build
- None — bug fixes only

### 📝 Step-by-step implementation plan
1. Write a simple test checklist covering: valid ticker search (multiple sectors), invalid/misspelled ticker, empty search submission, very long company names (layout check), stocks with missing/null data fields, slow network simulation (browser dev tools → Network tab → throttle to "Slow 3G") to see how loading states hold up.
2. Go through the checklist methodically, noting every bug/rough edge in a simple list (severity: must-fix vs. nice-to-fix).
3. Fix all must-fix bugs first.
4. Test in at least two different browsers (e.g., Chrome and Firefox, or Chrome and Safari) to catch any rendering inconsistencies.
5. Test on a narrow/mobile browser width one more time, end to end.
6. If time remains after must-fix bugs, address nice-to-fix items.

### 📂 Files and folders to create or modify
- Any file with bugs — likely `script.js` (logic fixes) and `style.css` (layout fixes)
- Optionally create `TESTING.md` to log what was tested and found (useful for your capstone documentation/demo credibility)

### 🔗 APIs, libraries, services, or tools to integrate
- None new — browser dev tools (Network throttling, console) are your main testing tools today

### 🧪 Testing tasks
- Execute the full checklist from step 1 above
- Re-test every bug fix to confirm it's actually resolved and didn't introduce a new issue

### 🐞 Common issues and debugging tips
- **A fix in one place breaks another**: after any fix touching shared functions (like formatting helpers), re-test all sections that use them, not just the one you were fixing.
- **Can't reproduce a bug consistently**: check if it's data-dependent (only happens for stocks missing a specific field) — test with that stock specifically.

### ✅ End-of-day checklist
- [ ] Full test checklist executed
- [ ] All must-fix bugs resolved and re-verified
- [ ] Tested in 2+ browsers and at narrow/mobile width
- [ ] `TESTING.md` created (optional but recommended) logging what was tested
- [ ] Code committed and pushed

### 📸 Expected project state and screenshots to capture
- Screenshot of the app working correctly on a narrow/mobile-width browser
- Screenshot of your test checklist/log (even if it's a simple text list)

### ➡️ Handoff notes for Day 9
Confirm the app is bug-free (or only has known, non-blocking nice-to-fix items remaining) and ready to deploy. Day 9 is entirely about getting this live on GitHub Pages and verifying the real, publicly-deployed version works exactly like the local version did.

---

## Day 9 — Deployment to GitHub Pages

> **Note (added Day 6):** Initial deployment was completed early, on Day 6, to satisfy that session's live-demo requirement. Day 9 now focuses on **regression testing the already-live site** after Days 7–8's changes (stretch features and bug fixes), rather than first-time deployment setup. Steps below remain valid for verifying any new pushes still deploy correctly.

### 🎯 Objective
Take the tested, working local app and make it live on the public internet via GitHub Pages, then verify the deployed version thoroughly (deployed environments sometimes behave differently than local).

### 📖 What I'll learn
- How GitHub Pages deployment actually works (branch settings, build source)
- Debugging environment-specific issues (paths, CORS behaving differently on a live domain vs. localhost)
- Basic post-deployment verification practices

### 🛠 Features to build
- None — deployment configuration only

### 📝 Step-by-step implementation plan
1. Ensure all code is committed and pushed to the `main` branch (or your default branch) on GitHub.
2. In the GitHub repo: go to **Settings → Pages**. Under "Build and deployment," set Source to "Deploy from a branch," choose `main` branch and `/ (root)` folder (adjust if your `index.html` is elsewhere), then Save.
3. Wait 1–2 minutes, then visit the provided GitHub Pages URL (format: `https://<username>.github.io/<repo-name>/`).
4. **Verify thoroughly on the live URL, not just locally**: test the same checklist from Day 8 again, specifically watching for CORS issues (some APIs behave differently when called from a real domain vs. `localhost`) and broken relative file paths (a common GitHub Pages gotcha if paths assume root when the site is actually served from a subfolder).
5. Fix any deployment-specific issues (usually path or CORS related) and re-push — GitHub Pages auto-redeploys on push to the configured branch within a minute or two.
6. Once stable, do a final full walkthrough on the live URL and take your official "it's live" screenshots.

### 📂 Files and folders to create or modify
- Possibly `index.html`/`script.js` if relative paths need adjusting for GitHub Pages' folder structure
- `README.md` — add the live URL and a short project description

### 🔗 APIs, libraries, services, or tools to integrate
- GitHub Pages (via repo Settings — no new code library)

### 🧪 Testing tasks
- Full Day-8-style checklist re-run, but on the live public URL
- Specifically check: does the API call still work from the live domain? (CORS policies sometimes only allow `localhost` in testing — verify this explicitly)

### 🐞 Common issues and debugging tips
- **Blank page on live URL but works locally**: almost always a relative path issue (e.g., `src="script.js"` needs to correctly resolve given the repo's subfolder structure) — check browser console for 404s on assets.
- **API works locally but fails live (CORS)**: some APIs/proxies whitelist `localhost` differently than production domains — if this happens, this is exactly the scenario the Day 2 fallback plan (static JSON) exists for; activate it if needed rather than losing a day debugging a dead end.
- **Changes not showing up**: GitHub Pages can take 1-2 minutes to redeploy, and browsers cache aggressively — hard-refresh (Ctrl/Cmd+Shift+R) before assuming a fix didn't work.

### ✅ End-of-day checklist
- [ ] GitHub Pages is enabled and serving the site
- [ ] Live URL tested with the full Day 8 checklist and passes
- [ ] Any deployment-specific bugs fixed and re-verified live
- [ ] `README.md` updated with live link and short description
- [ ] "It's live" screenshots captured

### 📸 Expected project state and screenshots to capture
- Screenshot of the live GitHub Pages URL in a browser address bar with the working app visible
- Screenshot of GitHub repo Settings → Pages showing the active deployment

### ➡️ Handoff notes for Day 10
The product is live and verified. Day 10 has no new building — it's final polish (if any small issues remain), documentation, and preparing to confidently demo/present the finished v1.0, including the pitch deck.

---

## Day 10 — Final Polish, Documentation & Demo Readiness

### 🎯 Objective
Cross the finish line: final polish pass, complete documentation, and a confident, rehearsed demo of the live v1.0 product — this is the capstone finale.

### 📖 What I'll learn
- How to write a clear, professional README for a portfolio project
- How to demo a product confidently and concisely
- Reflecting on the build process to articulate what you learned (valuable for the challenge writeup and your own growth)

### 🛠 Features to build
- None — documentation and demo prep only. Resist any last-minute new-feature urges today.

### 📝 Step-by-step implementation plan
1. Do one final walkthrough of the live site looking only for small visual polish issues (typos, spacing, color contrast) — fix only what's quick and low-risk this late in the process.
2. Finalize `README.md` with: project name, one-paragraph description, live link, screenshot, feature list (core + any stretch shipped), tech stack used, and a "What I'd build next" section (naturally pulls from the "out of scope" list from Day 1).
3. Do a final check that the PRD, this blueprint, and the pitch deck (all from Day 1) still accurately reflect what was actually built — note any deviations honestly (e.g., "chart stretch goal was descoped due to data limitations" is a perfectly good, honest note).
4. Rehearse a 2–3 minute demo out loud: problem → who it's for → live demo of searching a real stock → what makes it useful → what's next. Time yourself.
5. Do a final git commit/push and confirm the live URL is stable and working one last time, ideally a few hours before you need to present.

### 📂 Files and folders to create or modify
- `README.md` (finalized)
- Any last small polish fixes across `index.html`/`style.css`/`script.js`

### 🔗 APIs, libraries, services, or tools to integrate
- None new

### 🧪 Testing tasks
- One final full run-through of the live site as if you were a stranger using it for the first time

### 🐞 Common issues and debugging tips
- **Urge to add "just one more feature"**: resist — Day 10 is about shipping and presenting what exists, not expanding scope at the last minute.
- **README feels thin**: pull directly from the PRD's problem statement and feature list — you already have the content, just adapt the tone to be reader-friendly.

### ✅ End-of-day checklist
- [ ] Final polish pass complete (small fixes only)
- [ ] README.md finalized with live link, screenshots, features, tech stack, and future scope
- [ ] Demo rehearsed and timed (2-3 minutes)
- [ ] Live site double-checked and stable
- [ ] Capstone v1.0 is complete and deployed 🎉

### 📸 Expected project state and screenshots to capture
- Final full-page screenshot of the live, polished v1.0 product
- Screenshot of the finalized README on GitHub

### ➡️ Handoff notes
This is the final day — no further handoff needed. If continuing past the capstone, the natural next steps are exactly the "stretch goals not shipped" and "explicitly out of scope" items documented in the PRD (e.g., saved watchlists, more sectors' worth of red-flag tuning, native mobile app).

---

## How to Use This Blueprint in Future Daily Conversations

At the start of each new day's conversation, paste in (or summarize):
1. This day's section from this blueprint
2. A one-line status update ("Day 2 done, data source confirmed working, using X API" etc.)
3. Any deviations from plan so far

This is sufficient for a fresh AI conversation to pick up building immediately without re-litigating earlier decisions.