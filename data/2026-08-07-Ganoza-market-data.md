# Stage 4 Market Data and Population Memo

**Retrieval date:** 2026-08-07 (market close inputs)

| Input | Value used | Source and timestamp | Method / rationale |
|---|---:|---|---|
| `FC_AMT` | EUR 4,500,000 | Scenario | Unchanged scenario notional. |
| `S0_in` | 1.1535 USD/EUR | [ECB EXR daily feed](https://data-api.ecb.europa.eu/service/data/EXR/D.USD.EUR.SP00.A?startPeriod=2026-08-07&endPeriod=2026-08-07&format=csvdata), 2026-08-07, 14:15 CET | ECB EUR/USD reference rate. |
| `R_USD` | 4.06% | [FRED DGS1](https://fred.stlouisfed.org/series/DGS1), observation 2026-08-06, retrieved 2026-08-07 | Latest available 1-year U.S. Treasury constant-maturity yield; used as the USD one-year government-rate proxy. |
| `R_FC` | 2.25% | [ECB monetary-policy decision](https://www.ecb.europa.eu/press/pr/date/2026/html/ecb.mp260723~29f24d99bc.en.html), 2026-07-23, retrieved 2026-08-07 | ECB deposit-facility rate, unchanged as of the retrieval date; used as the EUR reference-rate proxy because a matched euro-area 1-year government series was not used. |
| `T_DAYS` | 358 days | Calendar calculation | 2026-08-07 to the 2027-07-31 settlement date. |
| `F0_in` | 1.1738 USD/EUR | CIP-implied, 2026-08-07 | `1.1535 × (1 + 0.0406 × 358/360) / (1 + 0.0225 × 358/360) = 1.1738079684`; no live one-year forward quote used. |
| `K_PUT`, `K_CALL` | 1.1535 USD/EUR | Derived from spot | At-the-money strikes, per the Stage 4 scenario convention. |
| `PREM_PUT`, `PREM_CALL` | 0.0200 USD/EUR | Scenario assumption | Retained as instructed; retail FX-option quotes are not sufficiently reliable for this exercise. |

## Population and checks

All live values were entered only through the workbook input cells/named ranges. The parity control passes: the implied forward is `1.1738079684`, and the `F0_in` rounding difference is `-0.0000079684 USD/EUR`, inside the `0.0001` tolerance. The forward versus money-market proceeds diagnostic is `$36`, attributable solely to rounding `F0_in` to four decimals.

## FX Hedging Lab reconciliation

The course FX Hedging Lab was populated with the same ten inputs. It returned forward proceeds of `$5,282,100`, money-market proceeds of `$5,282,136`, and reported a `$36` rounding difference. Those figures match the workbook, so no structural discrepancy remains.

## Stage 4 limitation

The forward is a theoretical CIP rate rather than an executable dealer quote. A live dealer forward may differ because of bid/ask spread, credit, and cross-currency basis; that distinction is explicit and should be revisited for the Stage 5 recommendation.
