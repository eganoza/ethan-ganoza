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
| F0_in | Forward rate | USD per EUR | 1.1220 (placeholder — parity-implied from interest rates: S0 × (1+R_USD×T/360)/(1+R_FC×T/360) ≈ 1.1220) |
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
- Options — EUR-receivable put payoff table across S_T scenarios, premiums and net proceeds; separate EUR-payable call reference table if retained
- Sensitivity — ±5% in 1% steps around S0_in (formula‑driven table and chart)
- Checks — parity check and other validation rules (§7) visible and passing
- Notes — assumptions, data provenance, and references

4. Assumptions & constraints (explicit)

- Rate basis: ACT/360 (use T_DAYS/360 in interest calculations)
- Interest compounding: simple interest over the settlement period (no intra‑period compounding) consistent with the MM flow formulas
- Premiums: PREM_* are expressed in USD per EUR and are treated as upfront cash costs deducted from net proceeds in USD
- Put-strike decision: `K_PUT = 1.00` with `PREM_PUT = 0.02` produces a net USD floor of $4,410,000 on the €4.5M receivable. This accepts a $0.10/EUR decline from `S0_in = 1.10` before the floor binds; the strike must be selected based on the business's required USD floor and documented as a risk-tolerance decision.
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

C. Options
- Define S_T scenarios (see §6 Sensitivity) as S_T = S0_in × (1 + pct_change)
- Put gross proceeds at settlement = FC_AMT × max(S_T, K_PUT)
- Put net proceeds = (FC_AMT × max(S_T, K_PUT)) − (FC_AMT × PREM_PUT)
- A long EUR call is not a hedge for this EUR receivable. If retained for comparison, display it only as an EUR-payable reference: call payoff = FC_AMT × max(S_T − K_CALL, 0); CALL_PAYABLE_COST = FC_AMT × S_T − call payoff + (FC_AMT × PREM_CALL). Do not include it in the receivable recommendation or net-proceeds chart.
- Output names: PUT_PROCEEDS_NET; CALL_PAYABLE_COST (reference only, if the call table is retained)

D. Parity / validation formula
- F_implied = S0_in × (1 + R_USD × T_DAYS/360) / (1 + R_FC × T_DAYS/360)
- PARITY_CHECK = F0_in − F_implied (USD per EUR)
- FORW_MM_PROCEEDS_DIFF = FORW_PROCEEDS − MM_PROCEEDS (USD; diagnostic output)
- Validation rule: ABS(PARITY_CHECK) <= CHECK_TOLERANCE, where CHECK_TOLERANCE is 0.0001 USD per EUR. This checks the quoted forward against parity directly; the USD proceeds difference is reported separately.

6. Sensitivity plan

- Create S_T grid: pct_change from −5% to +5% in increments of 1% (i.e., −0.05, −0.04, …, 0.05)
- Compute S_T = S0_in × (1 + pct_change) for each row
- For each S_T, compute USD_NO_HEDGE = FC_AMT × S_T, FORW_PROCEEDS (note: forward proceeds do not depend on S_T), MM_PROCEEDS (depends indirectly via S0_in only), and PUT_PROCEEDS_NET
- Chart: line chart comparing USD proceeds for No hedge, Forward, MM, and Put across S_T range. Exclude the payable-reference call series.

7. Validation rules (§7 checks) — explicit check figures

Include visible cells labeled with the check name and the expected relationship:
- Parity check passes: ABS(PARITY_CHECK) <= CHECK_TOLERANCE; display FORW_MM_PROCEEDS_DIFF separately as a diagnostic
- All summary outputs are formulas (no pasted numbers) — flagged in a sheet check (ISFORMULA test per output cell)
- Sensitivity recalculation: changing S0_in updates S_T table and all downstream outputs automatically

8. Outputs (named)

Summary outputs in gray cells, each with a named range:
- FORW_PROCEEDS
- MM_PROCEEDS
- USD_NO_HEDGE
- PUT_PROCEEDS_NET
- CALL_PAYABLE_COST (reference only, if retained)
- PARITY_CHECK
- FORW_MM_PROCEEDS_DIFF
- CHECK_TOLERANCE (input/assumption cell)

9. Prompt‑log evidence requirement

When using an AI to draft or build, include in prompt‑log.md at least one HIL (human‑in‑the‑loop) iteration: show an AI draft gap and the manual fix (before/after). Commit prompt‑log.md with the spec.

Filename & commit path

Save this spec as:

/docs/specs/2026-07-31-Ganoza-eur-receivable-hedge-spec.md

and commit it to the current branch so Stage‑3 can reference the committed spec URL.

End of spec.
