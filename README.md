# StockSense 📈

**Understand any Indian stock in one glance — no finance degree required.**

🔗 **Live app:** https://noobie007-sigma.github.io/stocksense/
📦 **Repo:** https://github.com/noobie007-sigma/stocksense

Built as a 10-day capstone for the **AB Talks 60-Day Claude AI Challenge**.

---

## What it does

Search any major NSE-listed Indian company by name or ticker, and instantly see a single, clean dashboard:

- **Company Snapshot** — price, market cap, sector, description
- **Key Ratios, Explained** — P/E, EPS, Dividend Yield, and Debt-to-Equity, each in plain English with a Good / Average / Concerning indicator
- **Red Flags** — specific, data-grounded concerns (or a clear "no major red flags" message)

No jargon. No logins. No navigating between pages — everything you need to make sense of a stock is visible on one screen.

## Screenshot

*(Add a screenshot of the live app here before sharing publicly — see `assets/og-image.png` for the social preview banner.)*

## Tech stack

- Plain HTML5, CSS3, vanilla JavaScript — no framework, no build step
- Data: a curated static JSON dataset (`data/stocks.json`) of 14 well-known NSE stocks — chosen deliberately over a live API, since free Indian stock APIs proved unreliable for browser-based use (see `docs/ARCHITECTURE.md` for the full reasoning)
- Hosting: GitHub Pages (free, static)
- No backend, no database, no accounts, no API keys, no cost

## Running it locally

1. Clone this repo: `git clone https://github.com/noobie007-sigma/stocksense.git`
2. Open the folder in VS Code
3. Install the **Live Server** extension
4. Right-click `index.html` → **Open with Live Server**

Full setup instructions: [`docs/SETUP.md`](docs/SETUP.md)

## Project documentation

This repo includes the full design and build history from a real 10-day product sprint:

- [`docs/PRD.md`](docs/PRD.md) — Product Requirements Document
- [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) — system design & tech stack rationale
- [`docs/SCHEMA.md`](docs/SCHEMA.md) — data structure
- [`docs/API.md`](docs/API.md) — internal function contracts
- [`docs/UI-WIREFRAMES.md`](docs/UI-WIREFRAMES.md) — user flow & wireframes
- [`docs/TESTING.md`](docs/TESTING.md) — QA checklist and results
- [`docs/Implementation_Blueprint_Day2-10.md`](docs/Implementation_Blueprint_Day2-10.md) — full day-by-day build log

## What's out of scope (by design)

No user accounts, no portfolio tracking, no real-time prices, no buy/sell execution, no native mobile app. This is an educational tool for a curious beginner, not a trading platform.

## What's next

- Connect a live stock data API (once a reliable free option exists for NSE/BSE)
- Sector-aware ratio thresholds
- Price history chart
- Broader beginner personas (total novices, not just the "curious beginner" persona this v1.0 targets)

## License

MIT — see [`LICENSE`](LICENSE).

## Credits

Built with [Claude](https://claude.com) as part of the AB Talks 60-Day Claude AI Challenge.