# University of Hawaiʻi at Mānoa · Shidler College of Business
## FIN 321: International Finance and Securities
### FX Transaction Hedging Project — Technical Specification

**Almond Packer — EUR Receivable Hedge Model**

| Field | Value |
|---|---|
| Created by | Remington Churchward |
| Updated by | Remington Churchward |
| Date Created | 2026-10-30 |
| Date Updated | 2026-11-06 |
| Version | 1.2 |
| LLM Used | ChatGPT GPT-6 (calculation review); Claude Opus 5.5 (gap review and revision, v1.2) |
| Role | Treasury Analyst / FP&A Analyst |
| Audience | CFO / Director of Treasury |
| Companion Workbook | `capabilities/fx-hedging/model.xlsx` |

---

## 1. Problem Statement

Almond Packer has a €2,400,000 receivable from a German confectionery customer, payable in 91 days: inception 2026-10-30, settlement 2027-01-29. Because the company's functional currency is USD, the dollar value of the receivable is exposed to EUR/USD movements until settlement. At today's spot of 1.1187 the receivable is worth $2,684,880. Every one-cent fall in EUR/USD costs $24,000, and a 5% euro decline would cost about $134,244. That shortfall would land on cash earmarked for grower payments and next season's operations. The primary objective is to protect the dollar value of the receivable; the secondary objective is to keep some upside if the euro strengthens, if that is affordable. This model compares an unhedged position, a forward contract, a money-market hedge, and a EUR put option so treasury can weigh certainty, flexibility, and cost before choosing a strategy.

## 2. Inputs — the Named-Range Contract

All inputs sit on the **Inputs** tab as workbook-level named ranges, formatted as yellow input cells. Every formula elsewhere refers to these names, never to cell addresses. Market values below are **placeholders: indicative, replaced with live market data at Stage 4.**

### 2.1 Core inputs (the ten standard names)

| Named range | Description | Placeholder value | Unit | Placeholder basis | Stage-4 data source |
|---|---|---:|---|---|---|
| `FC_AMT` | Foreign-currency receivable | 2,400,000 | EUR | Invoice (fixed) | Not replaced; invoice amount |
| `S0_in` | Spot rate at inception | 1.1187 | USD per EUR | Trading Economics, 2026-10-30 | Timestamped EUR/USD spot (Federal Reserve H.10 or Trading Economics); time recorded in Notes |
| `F0_in` | 91-day forward rate | 1.1653 | USD per EUR | Scenario indicative quote | Live 91-day EUR/USD forward (spot + forward points from a dealer or market data page), same timestamp as `S0_in` |
| `R_USD` | USD interest rate | 4.07% | Annual %, ACT/360 | 3-month CME Term SOFR, chosen by analyst | CME Term SOFR 3-month, latest publication |
| `R_FC` | EUR interest rate | 2.62% | Annual %, ACT/360 | 3-month Euribor (2026-10-28 fixing), chosen by analyst | EMMI 3-month Euribor, latest fixing |
| `K_PUT` | Put option strike | 1.1187 | USD per EUR | Set at spot (at the money), per scenario | Reset to the live `S0_in` value at Stage 4 |
| `K_CALL` | Call option strike | 1.1187 | USD per EUR | Set at spot, per scenario | Reset to live `S0_in`. **Contract-complete only; not used in a receivable hedge (see §5.5)** |
| `PREM_PUT` | Put premium per EUR | 0.0147 | USD | Scenario indicative | CME EUR/USD option settlement price or dealer quote matching `K_PUT` and expiry |
| `PREM_CALL` | Call premium per EUR | 0.0199 | USD | Scenario indicative | Same source as `PREM_PUT`, matching `K_CALL`. **Not used (see §5.5)** |
| `T_DAYS` | Days to settlement | 91 | Days | Scenario | Actual calendar days, inception date to settlement date |

### 2.2 Model parameters (blue cells; conventions, not market data)

| Named range | Description | Value | Unit |
|---|---|---:|---|
| `BASIS` | Day-count denominator, both legs (USD and EUR are both ACT/360) | 360 | Days |
| `STEP_FRAC` | Sensitivity step | 0.01 | Decimal (1%) |
| `GRID_RANGE` | Sensitivity half-width | 0.05 | Decimal (±5%) |
| `PARITY_TOL` | Maximum acceptable gap between `F0_in` and the parity-implied forward | 0.0050 | USD per EUR (0.5 cents, about $12,000 on `FC_AMT`) |

## 3. Tab Architecture

| Tab | Purpose |
|---|---|
| **Cover** | Title, company, exposure (`FC_AMT`, settlement date), author, version, date, and a one-line summary of the headline outputs (linked, not typed). |
| **Legend** | Color key: yellow = market input, blue = model parameter, black = formula, green = link to another tab, gray = named output. Lists every named range with its tab. |
| **Inputs** | The ten core inputs (§2.1) and four parameters (§2.2), each with unit, source, and access timestamp. The only tab an analyst edits. |
| **Forward** | Forward hedge calculation (§5.2). |
| **MoneyMarket** | Money-market hedge in three visible steps, the parity-implied forward, and the parity gap (§5.3). |
| **Put** | Premium carried to settlement, put payoff, floor, and breakevens (§5.4). |
| **Sensitivity** | The 11-row grid, hedge-profit columns, winner labels, and chart (§6). |
| **Checks** | Every validation rule in §7 with its expected value, actual value, and PASS / FAIL / FLAG result. |
| **Notes** | Assumptions (§4), data sources with retrieval date and time, and the change log. |

## 4. Assumptions and Constraints

- **Quote convention:** All exchange rates are USD per EUR. A higher rate means the euro has appreciated.
- **Exposure:** A EUR receivable, fully hedged (100% of `FC_AMT`) with one instrument at a time; each strategy is static from inception to settlement.
- **Day count:** Both rates are ACT/360 and use the simple accumulation form `1 + r × T_DAYS / BASIS`. The names `DF_USD` and `DF_FC` are growth factors (greater than 1), not discount factors.
- **Interest-rate parity:** Covered interest-rate parity says the forward and money-market hedges should produce the same USD. With the placeholder inputs they do not: `F0_in` sits about 4.25 cents above the parity rate (about $102,064). The model treats this as a **stale-quote flag**, not a formula error, and separately checks that the formulas themselves obey parity (§7, V5 vs. V6).
- **Option premium:** Paid upfront in USD at inception, quoted per 1 EUR with no contract multiplier. The premium is carried forward to settlement at `R_USD` so it is comparable with settlement-date proceeds. *Change from the Stage 1 brief:* the brief subtracted the premium without carrying it forward (floor $2,649,600, forward breakeven 1.1800). This spec carries it forward (floor $2,649,237.04, breakeven 1.180151).
- **Placeholders chosen by the analyst:** The two interest rates (Term SOFR and Euribor as the USD and EUR money-market rates) and the strikes (set at spot). Stage 4 replaces each with the source in §2.1.
- **Excluded:** Transaction costs and bid-ask spreads, counterparty and settlement risk, taxes and hedge accounting, rate changes during the hedge period, and probability weights on future spot rates. Option premiums are inputs, not priced from implied volatility.
- **Known risks to design against:** (1) The forward and option quotes may not share a timestamp and maturity with spot; Stage 4 must confirm they do. (2) A single `BASIS` works only because both legs are ACT/360; a GBP version would need separate `BASIS_USD` and `BASIS_FC`.

## 5. Calculation Flow

Named-range notation only. Each line below is its own cell with its own name; no nested single-cell versions.

### 5.1 Derived inputs (Inputs tab, below the input panel)

1. `DF_USD` = `1 + R_USD × T_DAYS / BASIS`
2. `DF_FC` = `1 + R_FC × T_DAYS / BASIS`
3. `FV_PREM_PUT` = `−PREM_PUT × FC_AMT × DF_USD` *(negative: a cost carried to settlement)*

### 5.2 Forward hedge (Forward tab)

- `USD_FWD` = `FC_AMT × F0_in`. Locked at inception; the same value in every sensitivity row.

### 5.3 Money-market hedge (MoneyMarket tab), three steps

1. **Borrow EUR today**, sized so the receivable exactly repays the loan at settlement: `MM_EUR_BORROW` = `FC_AMT / DF_FC`
2. **Convert at spot:** `MM_USD_NOW` = `MM_EUR_BORROW × S0_in`
3. **Invest in USD to settlement:** `USD_MM` = `MM_USD_NOW × DF_USD`

Parity diagnostics (same tab):

- `FWD_PARITY` = `S0_in × DF_USD / DF_FC` (the forward rate implied by covered interest-rate parity)
- `PARITY_GAP` = `F0_in − FWD_PARITY` (positive means the quote is above parity)
- `MM_FWD_GAP_USD` = `USD_FWD − USD_MM`
- `PARITY_FLAG` = `"STALE QUOTE"` if `ABS(PARITY_GAP) > PARITY_TOL`, otherwise `"OK"`

### 5.4 Put option hedge (Put tab)

At each settlement spot `S_T`:

- `USD_PUT(S_T)` = `MAX(S_T, K_PUT) × FC_AMT + FV_PREM_PUT`

Below the strike the company exercises and sells at `K_PUT`; above it the option expires and the company sells at `S_T`. The premium is paid either way. Scalar outputs on this tab:

- `USD_FLOOR_PUT` = `K_PUT × FC_AMT + FV_PREM_PUT` (the minimum proceeds)
- `BE_PUT_VS_FWD` = `F0_in − FV_PREM_PUT / FC_AMT` (the `S_T` above which the put beats the forward)
- `BE_PUT_VS_NOHEDGE` = `K_PUT + FV_PREM_PUT / FC_AMT` (the `S_T` below which the put beats staying unhedged)

### 5.5 Call option (specified for completeness, not built)

A EUR call protects a EUR **payable**: it caps the USD cost of buying euros. For a payable of `FC_AMT`, outlay = `MIN(S_T, K_CALL) × FC_AMT + PREM_CALL × FC_AMT × DF_USD`. Buying a call on top of a receivable would add euro exposure, not reduce it, so the workbook does not compute a call strategy. `K_CALL` and `PREM_CALL` stay on the Inputs tab to keep the ten-name contract complete.

## 6. Sensitivity Plan (Sensitivity tab)

**Grid.** `GRID_STEPS` = `ROUND(GRID_RANGE / STEP_FRAC, 0)` = 5. Rows run n = −`GRID_STEPS` … +`GRID_STEPS` (11 rows), with `S_T` = `S0_in × (1 + n × STEP_FRAC)`, from 0.95 × `S0_in` to 1.05 × `S0_in`. The n = 0 row is the base case.

**Columns, per row:**

| Column | Formula |
|---|---|
| `n` | Integer step |
| `S_T` | `S0_in × (1 + n × STEP_FRAC)` |
| No hedge | `FC_AMT × S_T` |
| Forward | `USD_FWD` |
| Money market | `USD_MM` |
| Put | `MAX(S_T, K_PUT) × FC_AMT + FV_PREM_PUT` |
| `HP_FWD` | Forward − No hedge |
| `HP_MM` | Money market − No hedge |
| `HP_PUT` | Put − No hedge |
| Overall winner | Label of the highest of the four proceeds columns |
| Best active hedge | Label of the highest of Forward, Money market, Put |

Labels are exactly `No hedge`, `Forward`, `Money market`, `Put`. If two values are equal to the cent, the label is `Tie`.

**Chart.** One XY scatter chart with straight lines (not a category line chart, so the x-axis spacing is true). X-axis: `S_T`, titled "EUR/USD at settlement (USD per EUR)". Y-axis: "USD proceeds at settlement". Four series: No hedge, Forward, Money market, Put. Title: "USD proceeds by hedge strategy, ±5% spot". The chart is linked to the grid and updates when inputs change.

**What the chart should let the CFO see:** The forward and money-market hedges are flat lines about $102,064 apart, which is the stale-quote gap. The put line is flat at its $2,649,237 floor below the strike, then rises parallel to the unhedged line, about $35,643 below it. Across the whole ±5% range the quoted forward is the best active hedge. Staying unhedged beats the forward only above 1.1653, and the put beats the forward only above 1.1802, which is outside the grid.

**Formatting:** No rounding inside calculations. Display `S_T` and rates to 4 decimals, interest rates to 2 decimal percentages, and USD to whole dollars with thousands separators.

## 7. Validation Rules (Checks tab)

Each rule shows expected, actual, and result. Expected values are for the placeholder inputs; after Stage 4, the rules marked † are recomputed from the live inputs, and the others must still hold.

| # | Rule | Expected (placeholders) | Tolerance | Result if outside |
|---|---|---|---|---|
| V1 | No cell anywhere in the workbook shows an error (`#DIV/0!`, `#NAME?`, `#REF!`, `#VALUE!`, `#N/A`) | 0 error cells | none | FAIL |
| V2 | Every output and grid cell is a formula; no typed numbers outside the Inputs tab | 0 hard-coded outputs | none | FAIL |
| V3 | All ten core names and four parameters exist and point to the Inputs tab | 14 names | none | FAIL |
| V4 | `DF_USD` / `DF_FC` † | 1.0102881 / 1.0066228 | ±0.0000001 | FAIL |
| V5 | **Formula parity:** `USD_MM` = `FC_AMT × FWD_PARITY` | Difference $0.00 | ±$1 | FAIL (formula error) |
| V6 | **Quote parity:** `ABS(PARITY_GAP)` ≤ `PARITY_TOL` | `PARITY_GAP` = 0.042527 → **flag expected** | 0.0050 | FLAG "STALE QUOTE" (not a model error) |
| V7 | `MM_FWD_GAP_USD` = `PARITY_GAP × FC_AMT` | $102,063.91 † | ±$1 | FAIL |
| V8 | Loan repays exactly: `MM_EUR_BORROW × DF_FC` = `FC_AMT` | 2,400,000.00 | ±€1 | FAIL |
| V9 | Money-market steps † | `MM_EUR_BORROW` 2,384,209.91; `MM_USD_NOW` 2,667,215.62; `USD_MM` 2,694,656.09 | ±$1 | FAIL |
| V10 | `USD_FWD` † | 2,796,720.00 | ±$1 | FAIL |
| V11 | Put kink: put proceeds at `S_T` = `K_PUT` equal `USD_FLOOR_PUT` † | 2,649,237.04 | ±$1 | FAIL |
| V12 | Breakevens † | `BE_PUT_VS_FWD` 1.180151; `BE_PUT_VS_NOHEDGE` 1.103849 | ±0.000001 | FAIL |
| V13 | Grid: 11 rows, n = 0 row equals `S0_in`, endpoints 0.95 and 1.05 × `S0_in` | 1.062765 / 1.118700 / 1.174635 † | ±0.000001 | FAIL |
| V14 | Forward and Money market columns are constant down the grid | max − min = 0 | $0 | FAIL |
| V15 | Spot-check rows † (see table below) | as listed | ±$1 | FAIL |
| V16 | Winner labels: Overall = `Forward` for n = −5…+4 and `No hedge` at n = +5; Best active hedge = `Forward` in every row † | as listed | exact | FAIL |

**V15 spot-check rows (placeholder inputs):**

| n | `S_T` | No hedge | Put | `HP_FWD` | `HP_MM` | `HP_PUT` |
|---:|---:|---:|---:|---:|---:|---:|
| −5 | 1.062765 | 2,550,636.00 | 2,649,237.04 | 246,084.00 | 144,020.09 | 98,601.04 |
| 0 | 1.118700 | 2,684,880.00 | 2,649,237.04 | 111,840.00 | 9,776.09 | −35,642.96 |
| +5 | 1.174635 | 2,819,124.00 | 2,783,481.04 | −22,404.00 | −124,467.91 | −35,642.96 |

## 8. Outputs (gray cells, named exactly)

| Named output | Tab | Placeholder value |
|---|---|---:|
| `USD_NO_HEDGE_BASE` (no hedge at `S_T` = `S0_in`) | Sensitivity | $2,684,880.00 |
| `USD_FWD` | Forward | $2,796,720.00 |
| `USD_MM` | MoneyMarket | $2,694,656.09 |
| `USD_PUT_BASE` (put at `S_T` = `S0_in`) | Put | $2,649,237.04 |
| `USD_FLOOR_PUT` | Put | $2,649,237.04 |
| `FWD_PARITY` | MoneyMarket | 1.122773 |
| `PARITY_GAP` | MoneyMarket | 0.042527 |
| `MM_FWD_GAP_USD` | MoneyMarket | $102,063.91 |
| `PARITY_FLAG` | MoneyMarket | STALE QUOTE |
| `BE_PUT_VS_FWD` | Put | 1.180151 |
| `BE_PUT_VS_NOHEDGE` | Put | 1.103849 |
| `SENS_TABLE` (the 11-row grid in §6) | Sensitivity | — |
| Sensitivity chart | Sensitivity | — |

The **Cover** tab repeats `USD_FWD`, `USD_MM`, `USD_FLOOR_PUT`, `MM_FWD_GAP_USD`, and `PARITY_FLAG` as green links. The recommendation itself belongs in the Stage 5 decision memo, not the workbook.

---

## Appendix — Change Log

| Version | Date | Author | Change |
|---|---|---|---|
| 1.0 | 2026-10-30 | Remington Churchward | Initial company-specific specification draft |
| 1.1 | 2026-11-06 | Remington Churchward | Refined calculations, named ranges, audit checks, and workbook requirements |
| 1.2 | 2026-11-06 | Remington Churchward | After AI gap review: reordered to the eight required sections; added Tab architecture and Validation rules with expected values; added `K_CALL`/`PREM_CALL`, the Stage-4 source column, and placeholder flags; added settlement date and dollar consequence; renamed `USD_PARITY` to `USD_MM` and named MM step 2 `MM_USD_NOW`; split the parity test into a formula check (V5) and a stale-quote flag (V6); aligned `PARITY_TOL` with the brief's 0.5 cents; replaced the text `GRID_RANGE` with a number; specified chart type, labels, tie rule, and gray named outputs; noted the premium-treatment change from the brief; removed template guidance sections |
