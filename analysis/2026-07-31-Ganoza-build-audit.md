# Stage 3 Audit Note: FX Hedge Workbook Rebuild

**Date:** 2026-08-07

**Workbook:** `models/builds/2026-07-31-Ganoza-eur-receivable-hedge-model.xlsx`

**Auditor:** Ethan Kainalu Ganoza
**Status:** PASS — rebuilt against the current Stage 2 specification

## Scope

This audit replaces the earlier Stage 3 note. The earlier workbook reflected the superseded Stage 2 design (including `F0_in = 1.1194`, a loose proceeds-based parity control, and a call shown as receivable proceeds). The rebuilt workbook follows the current committed specification.

## Checks Performed

| Check | Result | Evidence |
|---|---|---|
| Required input names | PASS | All 10 inputs are defined: `FC_AMT`, `S0_in`, `F0_in`, `R_USD`, `R_FC`, `K_PUT`, `K_CALL`, `PREM_PUT`, `PREM_CALL`, and `T_DAYS`. |
| Forward / money-market tie-out | PASS | `F_implied = 1.1219714071`; `F0_in = 1.1220`; rate difference = `0.0000285929`, within `CHECK_TOLERANCE = 0.0001 USD/EUR`. |
| Proceeds diagnostic | PASS | `FORW_PROCEEDS = $5,049,000`; `MM_PROCEEDS = $5,048,871`; difference = `$129`, attributable to the forward-rate rounding. |
| Money-market mechanics | PASS | EUR borrowing, spot conversion, and USD investment are separate visible formula rows. |
| Option treatment | PASS | The EUR receivable put remains in net-proceeds analysis. The EUR call is correctly labeled as a payable-only reference and excluded from the receivable sensitivity chart. |
| Sensitivity | PASS | Eleven formula-driven scenarios run from -5% to +5%; the chart shows No Hedge, Forward, Money Market, and Put. |
| Formula integrity | PASS | Programmatic formula scan found no `#REF!`, `#DIV/0!`, `#VALUE!`, `#NAME?`, or `#N/A` errors. Summary outputs and sensitivity rows are stored as formulas. |
| Presentation | PASS | Cover, legend, input, calculation, output, checks, notes, and chart tabs were visually inspected for readability and chart labeling. |

## Formula-Check Note

The `Checks` tab includes Excel `ISFORMULA` controls for the forward, money-market, and put summary outputs. The workbook-rendering engine used for this audit does not evaluate `ISFORMULA`, so those cells display `VERIFY IN EXCEL` in the render rather than an error. Direct workbook inspection confirmed the referenced cells contain formulas, and Excel will evaluate the controls when opened.

## Stage 4 Readiness

The workbook is ready for live-market-data population. All current market inputs remain explicitly labeled as placeholders. Stage 4 must document the as-of date, source, quote convention, and any forward basis or bid/ask spread before a recommendation is made.
