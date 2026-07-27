# API.md — StockSense Data Access Interface

**Status:** Finalized Day 2.

## Important framing note

StockSense has **no network backend** — there is no server to call, so there are no HTTP endpoints in the traditional sense. What follows is the **internal data access interface**: the JavaScript functions in `script.js` that the UI calls to get data, structured like an API contract (purpose, input, output, errors) so implementation on later days is unambiguous. Every "request" below is a local function call, not a network request, except for the one-time `fetch()` of the static JSON file itself.

This keeps a real benefit of API-style design — clear contracts, predictable error handling — without the complexity of an actual backend we don't need.

---

## 0. The one real network call

### `loadStockDataset()`
- **Purpose:** Fetch and parse `data/stocks.json` once, cache it in memory for the session.
- **Request:** `fetch('data/stocks.json')` — same-origin, no parameters, no auth.
- **Response:** Parsed JSON object matching SCHEMA.md (`{ datasetMeta, stocks: [...] }`).
- **Validation:** Confirm response is valid JSON and `stocks` is a non-empty array before proceeding; if not, treat as a fatal load error.
- **Authentication:** None.
- **Error cases:**
  - Network/file error (shouldn't happen on GitHub Pages, but defensively handled) → show a page-level error: "Something went wrong loading stock data. Please refresh."
  - Malformed JSON → same page-level error, logged to console for debugging.

---

## 1. `searchStock(query)`

- **Purpose:** Find a single stock record matching the user's search input.
- **Request (function input):** `query: string` — raw user input from the search box (e.g. `"tcs"`, `"Tata Consultancy"`, `"  TCS  "`).
- **Response (function output):** A single `Stock` object (per SCHEMA.md) if found, or `null` if not found.
- **Validation:**
  - Trim whitespace, lowercase the query before comparing.
  - Reject empty/whitespace-only queries before searching — return `null` immediately with a distinct "empty query" flag so the UI can show "Please enter a company name or ticker" instead of a generic not-found message.
- **Matching logic:** Case-insensitive exact or partial match against `ticker`, `companyName`, and any string in `searchAliases`.
- **Authentication:** None.
- **Error cases:**
  - No match found → return `null`; UI renders the "Couldn't find that stock — check the spelling or try a different ticker" state.
  - Empty query → return `null` with the empty-query flag; UI renders "Please enter a company name or ticker."

---

## 2. `getRatioAssessment(ratios)`

- **Purpose:** Convert raw ratio numbers into the Good / Average / Concerning indicator plus plain-English explanation text, per PRD §6.2.
- **Request (function input):** `ratios: object` — the `ratios` object from a matched `Stock` (per SCHEMA.md).
- **Response (function output):** An array of ratio-row view-objects, one per ratio, e.g.:
  ```json
  [
    {
      "label": "P/E Ratio",
      "value": 28.4,
      "displayValue": "28.4",
      "explanation": "How much investors are paying for each ₹1 of profit. Lower can mean better value, but very low can also signal market doubts.",
      "status": "Average",
      "statusColor": "yellow"
    }
  ]
  ```
- **Validation:** Any `null` ratio field must produce a row with `displayValue: "Data not available"`, `status: "Unknown"`, and a neutral color — never a crash or a fabricated "Good/Concerning" label.
- **Authentication:** None.
- **Error cases:** Missing `ratios` object entirely → return an empty array; UI shows "Ratio data not available for this stock" instead of an empty section.

---

## 3. `getRedFlags(stock)`

- **Purpose:** Derive the red-flags list from a matched stock's data, per PRD §6.3.
- **Request (function input):** `stock: object` — the full matched `Stock` object.
- **Response (function output):** An array of flag strings (each grounded in a specific value), or an empty array if no flags trigger.
  ```json
  ["Debt-to-Equity (1.8) is notably higher than typical for this sector."]
  ```
- **Validation:** Each rule must reference a real field value in its message (no vague flags); if the underlying field is `null`, that specific rule is skipped (not flagged as a concern, since absence of data isn't evidence of a problem).
- **Authentication:** None.
- **Error cases:** No flags triggered → return `[]`; UI renders the positive "No major red flags detected" state (never an empty, unexplained blank section).

---

## 4. `formatCurrency(value)` / `formatLargeNumber(value)`

- **Purpose:** Presentation-layer helpers — convert raw numbers into Indian-convention display strings (₹, Lakh/Crore).
- **Request (function input):** `value: number`
- **Response (function output):** `string`, e.g. `formatLargeNumber(1391000000000) → "₹13.91 Lakh Cr"`
- **Validation:** `null`/`undefined`/`NaN` input → return `"N/A"`, never `"₹NaN"` or a broken string.
- **Authentication:** None.
- **Error cases:** N/A (pure formatting function, no failure mode beyond invalid input handled above).

---

## 5. Summary Table

| Function | Purpose | Can Fail? | Failure Handling |
|---|---|---|---|
| `loadStockDataset()` | Load the dataset once | Rarely (file missing) | Page-level error message |
| `searchStock(query)` | Find a stock | Yes (no match / empty query) | Friendly inline error states |
| `getRatioAssessment(ratios)` | Explain ratios | Only if `ratios` missing entirely | Empty-state message, per-field "Data not available" |
| `getRedFlags(stock)` | Surface concerns | Never "fails" — empty array is a valid, positive result | Renders "no flags" positive state |
| `formatCurrency` / `formatLargeNumber` | Display formatting | Only on bad input | Returns `"N/A"` |

This interface is intentionally small and fully covers every v1.0 core feature (Snapshot, Ratios, Red Flags) with clear, predictable behavior for every edge case identified during design.