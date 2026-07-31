# challenge-retrospective.md — StockSense

**A 10-day journey from a vague interest in finance to a live, production-ready product.**

---

## Timeline: Day 1 → Day 10

**Day 1 — Discovery.** Started with two broad interests (music, finance) and no project idea. Through a structured interview, narrowed to: investing feels intimidating specifically at the "I found a stock, now what?" moment. Chose the persona deliberately narrow — a curious beginner with no finance background, not "everyone." Locked v1.0 scope to three core features (Snapshot, Ratios, Red Flags) with three stretch goals explicitly deprioritized in rank order. Produced the PRD, a 9-day Implementation Blueprint, and a pitch deck — before writing a single line of code.

**Day 2 — The first real pivot.** Research revealed NSE/BSE have no official public API. Rather than burn a day testing fragile CORS workarounds, the decision was made to make a curated static JSON dataset the *primary* v1.0 data source, not a fallback — trading real-time data for guaranteed reliability. This single decision shaped everything after it: no backend, no API keys, no auth, no database. Full system design followed: architecture diagrams, data schema, an internal "API" contract for a project with no real API, wireframes, and folder structure.

**Day 3 — Foundation.** Live Server installed, first 6 stock records hand-compiled, and the core data layer (`loadStockDataset`, `searchStock`) built exactly to the Day 2 spec. Because the live-API risk had already been eliminated, Day 3 finished with room to spare compared to the original plan.

**Day 4 — First real feature.** The Company Snapshot card — the first thing that actually looked like a product. Indian numbering conventions (Lakh/Crore) were built in from the start, a small but deliberate detail matching the actual target user.

**Day 5 — The ratio engine.** P/E, EPS, Dividend Yield, Debt-to-Equity, each translated into plain English with a Good/Average/Concerning verdict. The real design challenge here wasn't the thresholds — it was making sure missing data (`null`) was never confused with a bad value (`0`) or crashed the UI. That null-safety discipline, established on Day 5, held up through every day that followed.

**Day 6 — MVP complete, and an early deployment.** Red Flags shipped, completing the entire core feature set in exactly four building days (4–6). Deployment, originally scheduled for Day 9, was pulled forward because that session explicitly required a live, shareable demo — a real example of adapting the plan to new information rather than following it rigidly.

**Day 7 — Refinement, not more features.** With deployment already secured, the checkpoint decision was made deliberately: polish over stretch goals. Quick-search chips, a skeleton loader, accessible icon+color badges, keyboard focus states — the unglamorous 20% that makes a demo feel finished rather than merely functional.

**Day 8 — The bug bash that mattered.** A full senior-QA-style review caught something real: market cap figures across the dataset were roughly 10x too small — a data accuracy bug, not a cosmetic one, in a tool whose entire value proposition is trustworthy financial information. Also fixed: a favicon encoding issue, a fetch race condition, an inconsistent search-matching rule, and CSS overflow edge cases. Every issue found was fixed same-day, none deferred.

**Day 9 — Production readiness.** The parts that matter once strangers actually click the link: social preview metadata and a custom banner image, a skip-to-content accessibility link, a custom 404 page, an MIT license, and a README rewritten to actually tell the project's story.

**Day 10 — Launch.** Final polish (real screenshot, GitHub topics/metadata), a proper v1.0.0 release, and this retrospective.

---

## Major technical decisions and pivots

1. **Static data over live API (Day 2)** — the single most consequential decision in the project. Removed the biggest risk, simplified the entire architecture, and was made transparently as a deviation from the original plan rather than silently.
2. **Deployment moved earlier (Day 6)** — the blueprint adapted to a real external requirement instead of being followed blindly.
3. **Polish over stretch features (Day 7)** — a deliberate, reasoned trade-off given the project's context that day, not a default.
4. **Fixing a data accuracy bug over adding features (Day 8)** — correctness was prioritized over velocity once a real trust-affecting bug was found.

## Challenges solved / notable debugging moments

- Diagnosing a "blank deployed page" (Day 6) that turned out to be a propagation/cache delay, not a real bug — resolved by systematic checking (console, exact URL, hard refresh) rather than guessing.
- A user confusing the custom 404 page with the in-app "stock not found" state (Day 9) — a genuinely useful reminder that "URL doesn't exist" and "search had no match" are different failure modes that deserve different UI.
- The Day 8 market-cap bug — caught only because of a deliberate, adversarial senior-QA review pass, not regular use, underscoring why dedicated testing days matter even on a solo project.

## Skills demonstrated across the build

Product discovery and scope negotiation (Day 1) · technical architecture and trade-off reasoning under real constraints (Day 2) · incremental, testable feature delivery (Days 3–6) · defensive programming and null-safety discipline (Day 5) · UX/accessibility craft (Day 7) · systematic QA and bug triage (Day 8) · production-readiness and technical SEO (Day 9) · release management and portfolio storytelling (Day 10).

## Final project summary

StockSense is a single-page, zero-cost, zero-backend web app that lets a financially-untrained beginner search any of 14 curated NSE-listed stocks and see a plain-English snapshot, ratio breakdown, and red-flag check — all on one screen. It shipped as a real, live, publicly accessible product, not a prototype, in 10 days at a 1–2 hour/day pace.

## Lessons learned

- **The best fallback is sometimes the primary plan.** Day 2's static-data decision felt like a compromise in the moment; in hindsight, it was the correct architecture for this project's actual constraints.
- **Deadlines reveal priorities honestly.** Day 6's early deployment and Day 7's polish-over-stretch choice both came from reacting truthfully to real constraints instead of forcing the original plan to fit.
- **Testing finds what usage doesn't.** The Day 8 market-cap bug had been live for two days without being noticed during normal use — dedicated adversarial review caught it.
- **Documentation compounds.** Because every day's decisions were written down as they happened, later days (and this retrospective) didn't require reconstructing "why did we do it this way" — it was already answered.

---

## A note from your AI pair programmer

Ten days ago you didn't have a project — you had two interests and a willingness to be interviewed about them. What you're closing out today is a real, live, publicly deployed application with a documented paper trail from problem discovery through a tagged v1.0.0 release. We hit a wall on Day 2 (no free live Indian stock API) and didn't pretend it wasn't a problem — we changed the plan, wrote down why, and kept moving. We found a real bug on Day 8 because we went looking for one, not because a user complained. That's the actual shape of shipping software, not the polished version — and you did it in the time you actually had, 1–2 hours a day, without cutting corners on honesty about what was and wasn't done. That's worth being proud of.