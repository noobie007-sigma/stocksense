# 30-Day Growth Plan — StockSense

A realistic, one-milestone-per-day roadmap taking StockSense from its current v1.0.0 (14 static stocks, 3 core features, no backend) toward the "3-month" horizon in `future-scope.md` — a real backend-backed data pipeline, ~100 stocks, sector-aware ratios, and the two deferred stretch features. Each day assumes the same 1–2 hour/day pace as the original capstone, and each day's output is something you can commit and see working — no day is purely planning.

Stack introduced along the way: a serverless proxy function (Cloudflare Workers free tier, since it needs no credit card for the free tier as of this writing — verify current terms when you start), Chart.js (already used in earlier stretch-goal planning), and an expanded JSON dataset. No framework migration is included in this 30 days — that would be a distinct, larger initiative, not part of this plan.

| Day | Milestone |
|---|---|
| 1 | Audit current ratio thresholds against 3 different sectors (IT, Banking, Metals) using public sources; document where "one-size-fits-all" thresholds clearly misjudge a sector-typical stock. |
| 2 | Design the `sectorAverages` schema addition (already stubbed in `SCHEMA.md`) with real compiled averages for the sectors currently in the dataset. |
| 3 | Implement sector-aware thresholds in `getRatioAssessment()` — pass sector into the function, adjust "Good/Average/Concerning" cutoffs per sector, fall back to today's generic thresholds for sectors without enough data. |
| 4 | Test sector-aware ratios against all 14 existing stocks; fix any cases where the new logic produces a worse/less intuitive verdict than the old flat thresholds. |
| 5 | Commit + deploy sector-aware ratios. This is a real, shippable v1.1 milestone on its own. |
| 6 | Research Cloudflare Workers (or equivalent) free tier for a lightweight serverless proxy; confirm current free-tier limits and signup requirements. |
| 7 | Set up a Cloudflare account and deploy a minimal "hello world" Worker, to de-risk the platform before building real logic into it (mirrors the Day 2 capstone approach of testing early). |
| 8 | Identify one candidate real data source for the proxy to call server-side (revisit Day 2's research — this time a server-side call sidesteps the CORS/key-exposure problems that ruled it out originally). |
| 9 | Build the Worker endpoint: accepts a ticker, calls the data source server-side, returns clean JSON in the same shape as `SCHEMA.md`'s `Stock` object. |
| 10 | Test the Worker directly (via browser or a tool like Postman/Thunder Client) for 5 different tickers; handle and log failures clearly. |
| 11 | Add a `USE_LIVE_DATA` feature flag in `script.js` — when true, `loadStockDataset`-equivalent calls the Worker per-search instead of loading the static file; when false, current static behavior is unchanged (never break the existing reliable path while building the new one). |
| 12 | Wire up live search behind the flag; test with the flag on, comparing live output to the static entry for the same ticker. |
| 13 | Add a graceful fallback: if the live call fails, fall back to the static dataset entry for that ticker if one exists, otherwise show a clear "live data unavailable" state — never a worse experience than today's v1.0. |
| 14 | Full regression test with the flag on: search 10+ tickers, invalid tickers, empty query, slow network simulation. |
| 15 | Ship live data behind the flag defaulted to **off** in production — this is a deliberate, cautious rollout step, not a full cutover yet. |
| 16 | Compile 20 more curated stock records (bringing the static dataset to ~35) to keep expanding reliable coverage in parallel with the live-data experiment. |
| 17 | Add the `priceHistory` field (already stubbed in `SCHEMA.md`) for the original 14 stocks, hand-compiled from public sources. |
| 18 | Integrate Chart.js via CDN; build a minimal line chart component reading `priceHistory`. |
| 19 | Style the price chart to match the existing card design system; add it as a new section in the render pipeline, following the same `render*()` function pattern as Snapshot/Ratios/Red Flags. |
| 20 | Test the price chart across stocks with and without `priceHistory` data — must degrade gracefully (hide the section, not show a broken chart) when data is missing. |
| 21 | Commit + deploy the price chart as v1.2. |
| 22 | Turn on the live-data flag for a small trial (e.g., just your own testing) and monitor for a few real searches across a day — this is where you decide, with real evidence, whether live data is trustworthy enough to become the default. |
| 23 | Based on Day 22's evidence, either flip `USE_LIVE_DATA` to default-on with static as fallback, or document clearly why static remains primary a while longer — either outcome is a legitimate, informed decision, not a failure. |
| 24 | Expand `sectorAverages` to cover the newly added stocks from Day 16. |
| 25 | Full accessibility re-check with a real screen reader (NVDA on Windows or VoiceOver on Mac) — go beyond the automated checks from the original capstone. |
| 26 | Add basic, privacy-respecting search analytics (e.g., a free tier of Plausible or a simple self-logged count) to see which tickers get searched most — this informs which stocks to prioritize adding next. |
| 27 | Write `docs/DATA-SOURCING.md` documenting exactly how the live data path works, its known limitations, and how to add new curated stocks manually — future-you (or a contributor) will need this. |
| 28 | Full regression pass across every feature: Snapshot, Ratios (sector-aware), Red Flags, Price Chart, live/static data paths, all responsive breakpoints. |
| 29 | Update the README, screenshot, and version number; tag a `v1.2.0` (or appropriate) GitHub release documenting everything shipped this month. |
| 30 | Write a 30-day retrospective (same spirit as `challenge-retrospective.md`) — what changed, what you learned about live-data reliability, and what the next 30-day plan should focus on (likely: user accounts, per `future-scope.md`'s 12-month horizon). |

**Ground rule carried over from the capstone:** if any day reveals a wall (e.g., the chosen data source turns out unreliable, same as Day 2's original discovery), the correct move is the same one that worked before — stop, document the real constraint, adjust the plan, and keep the core product experience (one search, one screen, plain English) intact no matter what changes underneath it.