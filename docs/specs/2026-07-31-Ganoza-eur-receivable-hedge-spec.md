# Stage 2 — Model Specification: EUR Receivable Hedge

Author: Ethan Kainalu Ganoza
Date: 2026-07-31
Scenario: EUR receivable due 2027-07-31

Summary

This Stage‑2 spec fully defines the workbook to be built in Stage‑3. It lists every input, named range, tab, calculation flow, sensitivity plan, and validation check in named‑range notation so an AI or analyst can construct the workbook without further clarification.

1. Problem statement

We expect to receive a foreign‑currency receivable of EUR 4,500,000 on 2027‑07‑31. The business reports in USD. The exposure is the USD value of the euro receivable, which varies with EUR/USD. The workbook will compare three hedge families (forward, money‑market hedge, options) and produce USD proceeds for each under scenarios.

2. Inputs — the named‑range contract (exact names to use)

All ten names below are mandatory and must be created as named ranges attached to their input cells. Placeholder values are indicative and flagged "(placeholder)".

| Named range | Description | Unit | Placeholder |
|-------------|-------------|------:|------------:|
| FC_AMT | Foreign‑currency receivable | EUR | 4,500,000 (placeholder) |
| S0_in | Spot rate at inception | USD per EUR | 1.10 (placeholder) |
| F0_in | Forward rate | USD per EUR | 1.0875 (placeholder) |
| R_USD | USD nominal annual interest rate | decimal (annual) | 0.035 (3.5%) (placeholder) |
| R_FC | Foreign (EUR) nominal annual interest rate | decimal (annual) | 0.015 (1.5%) (placeholder) |
| K_PUT | Put option strike | USD per EUR | 1.00 (placeholder) |
| K_CALL | Call option strike | USD per EUR | 1.20 (placeholder) |
| PREM_PUT | Put premium per unit of FC | USD per EUR | 0.02 (USD per EUR) (placeholder) |
| PREM_CALL | Call premium per unit of FC | USD per EUR | 0.02 (USD per EUR) (placeholder) |
| T_DAYS | Days to settlement from inception | days | 365 (placeholder) |

Notes: Units and placeholder sources must be documented: Stage‑4 sourcing will use ECB, Fed H.15, market forwards, and live option premium quotes (vendor TBD).

3. Tab architecture (required sheets)

- Cover — scenario, author, date, data‑provenance block (placeholders ok)
- Legend/Key — color convention: Yellow = inputs; Blue = assumptions; Green = formulas; Gray = outputs
- Inputs — all named inputs laid out with cells colored Yellow/Blue as appropriate and named ranges attached
- Forwards — single‑line forward calculation and detailed summary outputs
- MoneyMarket — three explicit steps (borrow FC, convert at spot, invest USD) with each step a visible row/cell
- Options — put and call payoff tables across S_T scenarios, premiums and net proceeds
- Sensitivity — ±5% in 1% steps around S0_in (formula‑driven table and chart)
- Checks — parity check and other validation rules (§7) visible and passing
- Notes — assumptions, data provenance, and references

4. Assumptions & constraints (explicit)

- Rate basis: ACT/360 (use T_DAYS/360 in interest calculations)
- Interest compounding: simple interest over the settlement period (no intra‑period compounding) consistent with the MM flow formulas
- Premiums: PREM_* are expressed in USD per EUR and are treated as upfront cash costs deducted from net proceeds in USD
- Transaction costs: ignored unless specified; document if added
- Dates: use ISO format (YYYY‑MM‑DD) in Cover and Inputs

5. Calculation flow (named‑range notation)

All calculated cells must reference named ranges. No nested literal constants.

A. Forwards
- Forward proceeds (USD) = FC_AMT × F0_in
- Output name: FORW_PROCEEDS (create as a named range in the workbook outputs area)

B. Money‑market hedge (three visible steps)
- MM_BORROW = FC_AMT / (1 + R_FC × T_DAYS/360)
  (Loan principal in EUR sized to be repaid by the receivable at settlement)
- MM_USD_NOW = MM_BORROW × S0_in
  (Convert the borrowed EUR to USD at spot)
- MM_PROCEEDS = MM_USD_NOW × (1 + R_USD × T_DAYS/360)
  (Invest USD now at R_USD until settlement; USD proceeds at settlement)
- Output name: MM_PROCEEDS

C. Options (put and call)
- Define S_T scenarios (see §6 Sensitivity) as S_T = S0_in × (1 + pct_change)
- Put gross proceeds at settlement = FC_AMT × max(S_T, K_PUT)
- Put net proceeds = (FC_AMT × max(S_T, K_PUT)) − (FC_AMT × PREM_PUT)
- Call gross proceeds at settlement = FC_AMT × max(S_T, K_CALL) (or, for the standard seller/holder logic, mirror notation — specify the desired payoff; here we’ll include call payoff for comparison)
- Call net proceeds = (FC_AMT × max(S_T, K_CALL)) − (FC_AMT × PREM_CALL)
- Output names: PUT_PROCEEDS_NET, CALL_PROCEEDS_NET

D. Parity / validation formula
- F_implied = S0_in × (1 + R_USD × T_DAYS/360) / (1 + R_FC × T_DAYS/360)
- Parity_check = FORW_PROCEEDS − MM_PROCEEDS (numeric difference)
- Validation rule: ABS(Parity_check) < tolerance (tolerance = 0.01 × FC_AMT or numeric rounding threshold; specify as a cell CHECK_TOLERANCE in the workbook)

6. Sensitivity plan

- Create S_T grid: pct_change from −5% to +5% in increments of 1% (i.e., −0.05, −0.04, …, 0.05)
- Compute S_T = S0_in × (1 + pct_change) for each row
- For each S_T, compute FORW_PROCEEDS (note: forward proceeds do not depend on S_T), MM_PROCEEDS (depends indirectly via S0_in only), PUT_PROCEEDS_NET, CALL_PROCEEDS_NET
- Chart: line chart comparing net USD proceeds for Forward, MM, Put, Call across S_T range

7. Validation rules (§7 checks) — explicit check figures

Include visible cells labeled with the check name and the expected relationship:
- Parity_check passes: ABS(FORW_PROCEEDS − MM_PROCEEDS) <= CHECK_TOLERANCE
- All summary outputs are formulas (no pasted numbers) — flagged in a sheet check (ISFORMULA test per output cell)
- Sensitivity recalculation: changing S0_in updates S_T table and all downstream outputs automatically

8. Outputs (named)

Summary outputs in gray cells, each with a named range:
- FORW_PROCEEDS
- MM_PROCEEDS
- PUT_PROCEEDS_NET
- CALL_PROCEEDS_NET
- PARITY_CHECK
- CHECK_TOLERANCE (input/assumption cell)

9. Prompt‑log evidence requirement

When using an AI to draft or build, include in prompt‑log.md at least one HIL (human‑in‑the‑loop) iteration: show an AI draft gap and the manual fix (before/after). Commit prompt‑log.md with the spec.

Filename & commit path

Save this spec as:

/docs/specs/2026-07-31-Ganoza-eur-receivable-hedge-spec.md

and commit it to the current branch so Stage‑3 can reference the committed spec URL.

End of spec.
