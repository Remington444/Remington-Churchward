EUR Receivable Hedge Brief: Almond Packer
Created by: Remington Churchward
Updated by: Remington Churchward
Date Created: 2026-10-30
Date Updated: 2026-10-30
Version: 1.0
LLM Used: Claude Opus 5.5

Executive Summary
We are owed EUR 2,400,000 by a German confectionery customer in 91 days. The euro amount is fixed; the dollars we receive are not. A 5% euro decline would cost us about $134,000. Three tools can protect this cash: a forward contract, a money-market hedge, or a put option. Before recommending one, I will build and test a model comparing them. My working hypothesis: the quoted forward beats the money-market hedge by about $102,000, because the forward quote looks stale against today's market.

Background & Objectives
At today's spot rate of 1.1187, the invoice is worth $2,684,880. Every one-cent move in EURUSD changes that by $24,000, and a 5% move in a quarter is ordinary for this currency pair. This cash funds grower payments and next season's operations, so a shortfall matters.
Primary objective: protect the dollar value of the receivable at an acceptable cost. Secondary objective: keep some upside if the euro strengthens, if it is affordable.

Methods
I will compare three hedge families on the same receivable:
Lock it in (forward). Agree today to sell EUR 2.4M at the indicative 91-day rate of 1.1653, fixing proceeds at $2,796,720. No upfront cost, but no gain if the euro strengthens.
Build our own lock (money-market hedge). Borrow euros today, convert at spot, and invest the dollars for 91 days; the customer's payment repays the loan. At today's rates this locks in about $2,694,656. Same certainty, but it uses credit lines and has more moving parts.
Buy insurance (EUR put). For $35,643 in premium, we buy the right to sell euros at today's spot. Net of premium, proceeds cannot fall below about $2,649,656, and we keep the upside. Like fire insurance, we pay whether or not we use it. It beats the quoted forward only if the euro ends above about 1.18.
The term sheet's call option protects a euro payable, not a receivable, so it is excluded.
Hypothesis: I expect the quoted forward to beat the money-market hedge by about $102,064, because the 1.1653 quote sits about 4.3 cents above the 1.1228 forward implied by covered interest parity. With a live quote, I expect the two to converge within a few thousand dollars.

Limitations & Next Steps
Assumptions:
The customer pays in full and on time, so currency is the only risk hedged.
We can trade at the quoted prices and have credit to borrow EUR 2.38M.
Rates: 3-month Term SOFR 4.07% (USD) and 3-month Euribor 2.62% (EUR), actual/360.
Weakest assumption: the forward and option quotes are current. The forward implies a spot near 1.16, against 1.1187 today.
How I would know I was wrong: if a live forward in Stage 4 still sits more than 0.5 cents (about $12,000) from the parity rate, parity is not holding in our prices, and the choice turns on costs and credit instead.
Next steps (owner: Remington Churchward):
Stage 2, model specification: design the workbook on paper.
Stage 3, AI-assisted build and audit: generate the workbook from the spec, then audit it.
Stage 4, live market data: replace placeholders with sourced, timestamped prices.
Stage 5, validation and decision memo: independent check and final recommendation.

References
CME Group. (2026). CME Term SOFR 3-month rate [Data set]. Retrieved October 30, 2026, from https://www.global-rates.com/en/interest-rates/cme-term-sofr/
European Money Markets Institute. (2026). 3-month Euribor, October 28, 2026 fixing [Data set]. Retrieved October 30, 2026, from https://euribor-rates.info/en/euribor-rate/3-month-rate/
Trading Economics. (2026). Euro US dollar exchange rate (EUR/USD) [Data set]. Retrieved October 30, 2026, from https://tradingeconomics.com/euro-area/currency

