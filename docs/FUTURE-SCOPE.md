# future-scope.md — StockSense

How this specific project could realistically evolve, broken into honest, achievable horizons rather than a wish list.

---

## 3 Months: Make the data real

The single biggest limitation of v1.0 is that `data/stocks.json` is a hand-compiled, periodically-updated snapshot of 14 stocks — not live data. The 3-month goal is to close that gap without reintroducing the risk that led to the Day 2 pivot away from a live API.

- **Move to a lightweight serverless backend** (e.g., a free-tier Cloudflare Worker or Vercel Edge Function) that proxies a real Indian market data source server-side, keeping any API key out of the browser entirely — solving the exact CORS/key-exposure problem identified on Day 2, this time with an actual backend instead of avoiding one.
- **Expand from 14 to ~100 stocks** covering the NSE Nifty 100, either via the new proxy or by scaling up the manual-compile approach if a live source still isn't reliable.
- **Add the deferred stretch goals from Day 7**: price history chart (Chart.js, already scoped in `SCHEMA.md`'s `priceHistory` field) and sector-average comparison (`sectorAverages` field, also already scoped but unused in v1.0).
- **Add a "last verified" badge** distinct from "last updated" — showing when a human last sanity-checked the figures, building on the data-honesty principle established in the PRD.

## 6 Months: Make it a real reference tool, not just a demo

- **Sector-aware ratio thresholds.** The current `RATIO_CONFIG` thresholds (e.g., "P/E under 15 = Good") are flagged in the PRD as an acknowledged oversimplification — a fair P/E for an IT services company and a capital-intensive utility are very different. This is the single highest-value quality improvement available, and it's a data/config change, not an architecture change.
- **A lightweight comparison view**: let a user look at two stocks side-by-side, reusing the exact same `renderSnapshot`/`renderRatios`/`renderRedFlags` functions, just rendered twice.
- **Basic search analytics** (which tickers are searched most) to guide which stocks get added next — using a free tier of a privacy-respecting analytics tool, not user tracking.
- **A11y audit with a real screen reader** (NVDA or VoiceOver), going beyond the automated/manual checks done Day 7–9.

## 12 Months: Make it a product, not a project

- **User accounts + saved watchlists** — the first genuinely new architectural layer, using a free-tier backend-as-a-service (e.g., Supabase's free tier) for auth and storage, introduced deliberately and only once the core analysis engine has proven itself with real users.
- **Broader personas**: a "total beginner" mode (even more hand-holding, glossary tooltips inline) and an "intermediate" mode (more ratios, less explanation) — as separate, chosen views rather than diluting the current single, focused persona.
- **Portfolio-level view**: aggregate red flags and ratio health across a user's saved list, not just one stock at a time.
- **Revisit build vs. buy on data**: by this point, a paid data API (previously ruled out per the "free tools only" constraint) might be justifiable if the project has real users — this is the first point where introducing cost is a reasonable trade-off to reconsider explicitly, not a shortcut taken early.

## What deliberately stays the same at every horizon

The core promise — one search, one screen, plain English, no navigating — is the product's identity, established directly from the founder's Day 1 answer to "what does success look like." Every idea above adds depth or reach; none of them should ever require adding a second screen, a settings menu, or jargon back into the ratio explanations.