# FX hedging

This capability compares ways to protect the dollar value of a foreign-currency receivable: staying unhedged, a forward contract, a money-market hedge, and a put option, across a ±5% range of settlement spot rates. It is specified in `spec.md` using a standard named-range contract, built as `model.xlsx`, and checked against the validation rules in the spec, including a covered-interest-parity test that flags a stale forward quote.

| File | What it is | Stage |
|---|---|---|
| `spec.md` | Model specification: inputs, tabs, calculation flow, sensitivity plan, validation rules, outputs | 2 |
| `model.xlsx` | Workbook built from spec v1.4: inputs, forward, money-market, put and call tabs, sensitivity grid and chart, and a Checks tab | 3 |

## Engagements

| Scenario | Company | Exposure | Horizon | Brief |
|---|---|---|---|---|
| 30, `almond-packer` | U.S. almond grower-packer invoicing a German confectionery maker | EUR 2,400,000 receivable | 91 days | [2026-10-30-almond-packer-hedge-brief.md](../../docs/briefs/2026-10-30-almond-packer-hedge-brief.md) |
