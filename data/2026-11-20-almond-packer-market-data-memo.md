# Market-Data Memo: Almond Packer EUR Receivable Hedge

**University of Hawaiʻi at Mānoa · Shidler College of Business**  
**FIN 321: International Finance and Securities**

| Field | Value |
|---|---|
| Author | Remington Churchward |
| Date | 2026-11-20 |
| Scenario | 30, almond-packer: EUR 2,400,000 receivable, 91 days |
| Data as of | Latest published market fixings as of 2026-11-19 (retrieved 2026-11-20) |
| Workbook | capabilities/fx-hedging/model.xlsx (commit 8561e78 populated, bc2cdbd fixed) |

## 1. Purpose

This memo documents the sources and assumptions used to replace the placeholder market inputs in the Almond Packer FX hedging model with live market data. It provides a record of where each input came from so the values can be independently verified and explains how the updated data affected the model's results. The analysis also includes a covered interest rate parity check and a comparison with the FX Hedging Lab.

## 2. Input provenance

| Named range | Value | Unit | Source | As of | Retrieved | Proxy / computation |
|---|---:|---|---|---|---|---|
| FC_AMT | 2,400,000 | EUR | Scenario 30 invoice | — | — | None |
| T_DAYS | 91 | days | Scenario 30 | — | — | Settlement 2027-02-18 (inception 2026-11-19 + 91) |
| S0_in | 1.0992 | USD per EUR | ECB euro reference rate, USD | 2026-11-19 fix | 2026-11-20 | None |
| R_USD | 4.20063% | annual, ACT/360 | 3-month CME Term SOFR via Global-rates.com | 2026-11-19 | 2026-11-20 | Proxy for the USD money-market rate |
| R_FC | 2.847% | annual, ACT/360 | 3-month Euribor (EMMI) via Euribor-rates.eu | 2026-11-19 fixing | 2026-11-20 | Proxy for the EUR money-market rate |
| F0_in | 1.1653 | USD per EUR | Bank's indicative 91-day forward quote, Scenario 30 | Scenario | — | Kept unchanged; checked against CIP in §4 |
| K_PUT | 1.0992 | USD per EUR | Set at live spot | 2026-11-19 | — | Equal to S0_in, per scenario convention |
| K_CALL | 1.0992 | USD per EUR | Set at live spot | 2026-11-19 | — | Equal to S0_in, per scenario convention |
| PREM_PUT | 0.0147 | USD per EUR | Scenario 30 | — | — | Assumption; retained from scenario |
| PREM_CALL | 0.0199 | USD per EUR | Scenario 30 | — | — | Assumption; retained from scenario |

All dated market inputs use November 19, 2026, so the model uses a consistent market-data snapshot rather than mixing exchange rates and interest rates from different dates. November 20 is the retrieval date, not the market-data date.

## 3. Choices and assumptions

- **Rate tenor (R_USD and R_FC):** I selected the three-month rates because the receivable has a 91-day settlement period, which is approximately three months. Using three-month rates better matches the hedge horizon than using one-year rates. The model also applies the rates over the actual 91-day period using the formula 1 + r × 91/360, rather than assuming a full year of interest.

- **Rate instruments:** CME Term SOFR and Euribor are reasonable benchmarks for estimating USD and EUR money-market rates. Term SOFR provides a forward-looking benchmark for U.S. dollar borrowing, while Euribor represents euro-denominated unsecured interbank funding. One limitation is that these benchmarks do not necessarily equal the rates an actual company would receive. A company may pay an additional credit spread or face different borrowing and investment rates depending on its financial position.

- **Forward:** I kept the original 1.1653 bank quote instead of replacing it with the parity-implied forward. The quote is part of Scenario 30 and is an input the model is supposed to evaluate, not a value that should be changed simply to make the parity check pass. Its large difference from the calculated parity rate is reported as a stale-quote flag in Section 4.

- **Option premiums:** The put premium of 0.0147 and call premium of 0.0199 are scenario assumptions, not live market quotes. I retained them because the assignment provides these values and the available market-data collection did not include verified option quotes matching the updated strikes and 91-day expiration. Actual option pricing would require a dealer quote or market data matching the contract terms.

- **Strikes:** Both option strikes were reset to the live spot rate of 1.0992. This makes both options at the money at inception, following the scenario's original convention. The call remains a payable illustration rather than a recommended receivable hedge.

## 4. Parity check: CIP-implied forward vs. the bank's quote

The covered interest rate parity forward was calculated using:

F0(CIP) = S0 × (1 + R_USD × T/360) / (1 + R_FC × T/360)

| Item | Value |
|---|---:|
| USD growth factor, 1 + 0.0420063 × 91/360 | 1.0106183 |
| EUR growth factor, 1 + 0.02847 × 91/360 | 1.0071966 |
| CIP-implied forward | 1.102934 |
| Bank's quote (F0_in) | 1.165300 |
| Gap, rate (quote − CIP) | +0.062366 (6.24 cents) |
| Gap, % of CIP forward | 5.65% |
| Gap, USD on EUR 2,400,000 | $149,678 |
| Tolerance (PARITY_TOL) | 0.50% |
| Result | FLAG: STALE QUOTE (check V6) |

The bank's quoted forward is approximately 6.24 cents above the parity-implied forward, creating a difference of approximately $149,678 on the full receivable. This exceeds the model's 0.50% tolerance and triggers the stale-quote flag, although the underlying money-market and parity formulas still pass their validation check. I did not request a bank requote for this assignment because the original quote was retained as a Scenario 30 input for evaluation; the flag identifies the need to obtain a current, maturity-matched dealer quote before any real transaction.

## 5. Model results with live data

| Output | Placeholder (Stage 3) | Live (Stage 4) |
|---|---:|---:|
| Spot S0_in | 1.1187 | 1.0992 |
| No hedge at today's spot | $2,684,880 | $2,638,080 |
| Forward | $2,796,720 | $2,796,720 |
| Money market | $2,694,656 | $2,647,042 |
| Put floor | $2,649,237 | $2,602,425 |
| Forward − money market | $102,064 | $149,678 |
| Put beats forward above S_T of | 1.180151 | 1.180156 |
| Put beats no hedge below S_T of | 1.103849 | 1.084344 |
| Sensitivity grid range | 1.062765–1.174635 | 1.044240–1.154160 |
| Checks: FAIL / FLAG | 0 / 1 | 0 / 1 |

The sensitivity grid and chart recalculated around the new spot rate, moving the 11 settlement-rate scenarios from the original range of 1.062765–1.174635 to 1.044240–1.154160. The no-hedge and money-market results decreased because the updated spot rate was lower, while the forward proceeds remained unchanged because the original forward quote and invoice amount were retained.

The put floor also decreased because its strike was reset to the lower live spot rate. Its premium remains an assumption, so the updated put result should not be treated as a market-priced option valuation.

## 6. Structural fixes

| # | What broke | How it was found | Fix | Commit |
|---|---|---|---|---|
| 1 | Cover provenance still read "Indicative; replaced with live data at Stage 4," and the Notes/Checks parity notes still quoted the placeholder $102,064 gap. | Reading the Cover and Notes after recalculation; no check caught it because V1–V19 test numbers, not labels. | Replaced the typed text with formulas linked to the Inputs tab and the parity outputs. | bc2cdbd |

This showed that the original workbook's validation checks focused on numerical calculations but did not verify whether explanatory labels and notes updated with the live inputs. A future check should also test important provenance labels and summary text to prevent outdated information from remaining in the workbook after recalculation.

## 7. FX Hedging Lab cross-check

| Output | Workbook | FX Lab | Difference | Resolution |
|---|---:|---:|---:|---|
| Forward | $2,796,720 | $2,796,720 | $0 | Agree |
| MM step 1, EUR borrowed | 2,382,852 | 2,382,852 | 0 | Agree |
| MM step 2, USD at spot | $2,619,230 | $2,619,230 | $0 | Agree |
| MM step 3, money market | $2,647,042 | $2,647,042 | $0 | Agree |
| Implied forward | 1.102934 | 1.1029 | Rounding | Agree |
| Parity gap | +$149,678 | −$149,678 | Sign only | Lab shows MM − forward; workbook shows forward − MM |
| Put at today's spot / floor | $2,602,425 | $2,602,800 | −$375 | Premium carried forward vs. paid upfront |
| Put at +5% (S_T = 1.15416) | $2,734,329 | $2,734,704 | −$375 | Same cause |
| Winner, all 11 rows | Forward | Forward | — | Agree |

The $375 difference in the put results comes from how the option premium is treated. The workbook carries the upfront premium forward to the settlement date, while the FX Hedging Lab subtracts only the original premium paid at inception. The arithmetic is 35,280 × 1.0106183 = $35,654.61, and $35,654.61 − $35,280 = $374.61, which explains the approximately $375 difference.

For this model, carrying the premium forward is the correct convention because the specification requires all proceeds and costs to be compared at the settlement date. The workbook therefore follows the convention established in the technical specification, even though its put results are slightly lower than those shown by the FX Hedging Lab.

Overall, the workbook and FX Hedging Lab agree on the forward, money-market hedge, implied forward, and winning strategy across all 11 sensitivity scenarios. The differences are explained by the sign convention for the parity gap, rounding, and the treatment of the option premium.

## 8. For Stage 5

- **Forward quote:** The 1.1653 forward quote needs further attention because it exceeds the parity-implied rate by 5.65%. Stage 5 should consider obtaining a new dealer quote that matches the live spot date and the 91-day maturity before making a final hedge decision.
- **Option premiums:** The put and call premiums are still scenario assumptions. Actual option quotes matching the updated strikes and expiration should be obtained before relying on the option results for a real hedging decision.
- **Updated spot and put position:** The lower spot rate reduced the dollar value of the receivable and lowered the put's floor to approximately $2.60 million. The put still provides downside protection, but its premium means it only beats remaining unhedged when the settlement spot falls below approximately 1.084344.

## Sources

- European Central Bank, [Euro foreign exchange reference rates – US dollar](https://www.ecb.europa.eu/stats/policy_and_exchange_rates/euro_reference_exchange_rates/html/index.en.html), November 19, 2026 fixing, accessed November 20, 2026.
- Global-rates.com, [3-month CME Term SOFR](https://www.global-rates.com/en/interest-rates/cme-term-sofr/2/term-sofr-interest-3-months/), November 19, 2026 rate, accessed November 20, 2026.
- Euribor-rates.eu, [3-month Euribor](https://www.euribor-rates.eu/en/current-euribor-rates/2/euribor-rate-3-months/), November 19, 2026 fixing, accessed November 20, 2026.
- FIN 321, Scenario 30 (Almond Packer).
- FX Hedging Lab, https://adamwstauffer.github.io/ai-lms/fxlab.html, accessed November 20, 2026.
- Almond Packer FX Transaction Hedge Model, Technical Specification, Version 1.5, November 20, 2026.
