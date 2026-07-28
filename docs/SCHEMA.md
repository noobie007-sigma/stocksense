# SCHEMA.md — StockSense Data Design

**Status:** Finalized Day 2. There is no traditional database in v1.0 — the "schema" below defines the structure of `data/stocks.json`, the single static data source the whole app reads from.

---

## 1. Why JSON, Not a Database

With ~15–20 curated stock records and no writes at runtime (the app never saves anything — it's read-only), a database would add setup complexity with zero benefit. A single, well-structured JSON array is simpler, free to host, and trivially fast to query with plain JavaScript `.find()`/`.filter()`.

---

## 2. Top-Level Structure

`data/stocks.json` is a single JSON object with one array: `stocks`.

```json
{
  "datasetMeta": {
    "lastCompiledDate": "2026-07-20",
    "sourceNote": "Compiled manually from public company filings and financial summaries for educational purposes. Not real-time data."
  },
  "stocks": [ /* array of Stock objects — see below */ ]
}
```

---

## 3. `Stock` Object — Fields

| Field | Type | Required | Description |
|---|---|---|---|
| `ticker` | string | Yes | NSE symbol, e.g. `"TCS"`. Primary lookup key. |
| `bseCode` | string | No | BSE numeric code, e.g. `"532540"`, shown as supplementary info. |
| `companyName` | string | Yes | Full legal/display name, e.g. `"Tata Consultancy Services Ltd."` |
| `sector` | string | Yes | e.g. `"Information Technology"` — used for display and grouping. |
| `description` | string | Yes | 1–2 sentence plain-English description of what the company does. |
| `price` | number | Yes | Last-known share price in ₹ (INR). |
| `marketCap` | number | Yes | Market capitalization in ₹ (raw number; formatted to Lakh/Crore at render time, not stored pre-formatted). |
| `lastUpdated` | string (date) | Yes | ISO date `"YYYY-MM-DD"` — when this specific record's figures were compiled. Shown in the UI per PRD §8 (data honesty). |
| `ratios` | object | Yes | See §4 below. |
| `searchAliases` | array of strings | No | Alternate names/spellings to match on search, e.g. `["tata consultancy", "tcs"]`. |

## 4. `ratios` Object — Nested Fields

| Field | Type | Required | Notes |
|---|---|---|---|
| `peRatio` | number \| null | Yes (nullable) | Price-to-Earnings ratio. `null` if not meaningful (e.g. company has negative earnings). |
| `eps` | number \| null | Yes (nullable) | Earnings per share, in ₹. |
| `dividendYield` | number \| null | Yes (nullable) | Percentage, e.g. `1.8` means 1.8%. |
| `debtToEquity` | number \| null | Yes (nullable) | Ratio; `0` is valid (debt-free) and distinct from `null` (unknown). |

**Design rule:** `null` always means "data not available," never a hidden `0`. The UI (per ARCHITECTURE.md rule engine) must render "Data not available" for `null` fields rather than treating them as zero or omitting the row silently.

---

## 5. Example Record

```json
{
  "ticker": "TCS",
  "bseCode": "532540",
  "companyName": "Tata Consultancy Services Ltd.",
  "sector": "Information Technology",
  "description": "India's largest IT services and consulting company, providing software, digital transformation, and outsourcing services globally.",
  "price": 3845.20,
  "marketCap": 1391000000000,
  "lastUpdated": "2026-07-15",
  "ratios": {
    "peRatio": 28.4,
    "eps": 135.5,
    "dividendYield": 1.6,
    "debtToEquity": 0.02
  },
  "searchAliases": ["tata consultancy services", "tcs"]
}
```

---

## 6. Relationships & Constraints

- This is a **flat, single-collection structure** — there are no relationships/foreign keys, since v1.0 has exactly one entity type (Stock) and no user-generated data linking to it.
- **Constraint: `ticker` must be unique** across the array — it's the primary lookup key.
- **Constraint: every numeric field must be a raw, unformatted number** (no "₹", no commas, no "Cr" suffixes stored) — all display formatting (Lakh/Crore conversion, ₹ symbol, decimal rounding) happens in `script.js` at render time, keeping the data layer clean and reusable if a real API is swapped in later.
- **Constraint: `ratios` object must always be present**, even if every field inside it is `null` — this guarantees the rule engine never crashes on a missing object.

---

## 7. Schema Validated Against PRD User Stories

| PRD Requirement | Covered By |
|---|---|
| Search by company name or ticker | `ticker`, `companyName`, `searchAliases` |
| Company snapshot: price, market cap, sector, description | `price`, `marketCap`, `sector`, `description` |
| Key ratios explained in plain English with Good/Average/Concerning indicator | `ratios.*` fields feed the rule engine (thresholds live in `script.js`, not in data — see API.md) |
| Red flags, data-grounded | Derived at runtime from `ratios.*` — no separate stored field needed |
| Data honesty (no false real-time claims) | `lastUpdated` + `datasetMeta.sourceNote` |
| (Stretch) Price chart/trend | **Not covered by current schema** — would require an additional `priceHistory` array field per stock; flagged below |
| (Stretch) Industry average comparison | **Not covered by current schema** — would require a separate `sectorAverages` object; flagged below |

### Stretch-goal schema additions (only build if Day 7 stretch mode is reached)
```json
"priceHistory": [ { "date": "2026-06-01", "close": 3700.10 }, ... ],
```
```json
"sectorAverages": { "Information Technology": { "peRatio": 24.1, "dividendYield": 2.0 }, ... }
```
These are deliberately **not** part of the v1.0 core schema so that core implementation (Days 4–6) never depends on data that might not get built.