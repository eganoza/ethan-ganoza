# AI Prompt Log — Phase 0

### Prompt 1: Portfolio Folder Setup
> **User Prompt:** "What Terminal commands can I run on Mac to create the portfolio folders and stub README files?"
> **AI Response:** Provided shell commands (`mkdir -p` and `touch`) to build the subdirectories (`docs/`, `models/`, `data/`, `analysis/`) and add stub text into every `README.md`.

### Prompt 2: Bio & Resume Markdown Formatting Help
> **User Prompt:** "Here is my bio details and raw text resume. Help me format them into clean, professional Markdown for my portfolio repository's README.md and RESUME.md files."
> **AI Response:** Generated formatted Markdown structures for `README.md` and `RESUME.md`, which I then reviewed, customized, and saved to the repo.

---

Prompt log — 2026-07-31T13:58:01-07:00

Spec URL: https://raw.githubusercontent.com/eganoza/ethan-ganoza/eganoza-add-eur-receivable-hedge-doc/docs/decisions/2026-07-31-Ganoza-eur-receivable-hedge-framing.md

Prompt (paste this as a single input to ChatGPT/Claude):

- Repo spec URL: https://raw.githubusercontent.com/eganoza/ethan-ganoza/eganoza-add-eur-receivable-hedge-doc/docs/decisions/2026-07-31-Ganoza-eur-receivable-hedge-framing.md
- Deliverable: an Excel workbook saved as models/builds/2026-07-31-Ganoza-eur-receivable-hedge-model.xlsx that strictly follows the grading contract below.

Grading contract (must be satisfied):
1) Create exactly these 10 named ranges and attach them to the indicated input cells:
   - S0 (spot EUR/USD) — input (yellow)
   - EUR_amount — input (yellow)
   - SettlementDate — input (yellow)
   - PlanningRate — assumption (blue)
   - ForwardRate — assumption (blue)
   - R_domestic — assumption (blue) (USD interest rate, decimal)
   - R_foreign — assumption (blue) (EUR interest rate, decimal)
   - OptionStrike — input (yellow)
   - OptionPremiumUSD — assumption (blue) (premium in USD)
   - HedgeNotional — input (yellow)

2) All calculations must be formulas referencing named ranges; no hard-coded numeric results in calculated cells.

3) Workbook structure:
   - Cover page: scenario, author, date, data-provenance block (placeholders ok).
   - Legend/Key tab with color convention: Yellow=inputs, Blue=assumptions, Green=formulas, Gray=outputs.
   - Separate tabs: Inputs, Forwards, MoneyMarket, Options, Sensitivity, Checks, Cover, Legend, and Notes.

4) Forwards tab: compute forward proceeds in USD for EUR_amount using named ranges and ForwardRate.

5) MoneyMarket tab: implement the three explicit steps (borrow EUR today, convert at spot, invest USD, repay EUR at settlement) with formulas and show proceeds in USD; each step labeled and color-coded.

6) Options tab: compute put and call payoffs at settlement S_T (spot scenarios), include OptionPremiumUSD cost in USD, show net proceeds formula-driven.

7) Sensitivity tab: create a ±5% sensitivity around S0 in 1% steps (from -5% to +5%), formula-driven table and chart showing net USD proceeds for each hedge.

8) Checks tab: implement parity check and the spec's §7 checks (visible cells) so they compute and pass.

9) Formatting: apply Legend colors consistently; named ranges attached to their cells.

10) Save workbook with only formulas (no macros), at models/builds/2026-07-31-Ganoza-eur-receivable-hedge-model.xlsx and return an export of key sheets as CSV if requested.

Prompt-log: include this prompt verbatim in prompt-log.md and commit it.

Audit expectation: after generation, perform a rigorous audit, record ≥3 findings, fix them, commit fixes, and produce analysis/2026-07-31-Ganoza-build-audit.md describing checks performed and fixes (include cell references and commit SHAs).

Output instructions to the AI:
- Provide the full workbook (attach file) or provide clear step-by-step formulas and cell placements so the workbook can be built exactly.
- If uncertain about any assumption or named-range detail, ask before producing.
- Do not hardcode results; use formulas only.
- Use ISO dates and decimal rate conventions.

End of prompt.

Notes:
- Chosen named ranges: S0, EUR_amount, SettlementDate, PlanningRate, ForwardRate, R_domestic, R_foreign, OptionStrike, OptionPremiumUSD, HedgeNotional.
- Expected workbook path: models/builds/2026-07-31-Ganoza-eur-receivable-hedge-model.xlsx

Next action for user: paste the prompt above into your chosen web AI (ChatGPT/Claude) and request an .xlsx workbook. Ask the AI to attach the workbook or return a downloadable link. Once you have the workbook, upload it here or place it at the repo path above and tell me so I can audit and commit fixes.

---

## Stage 3 Build Cycle — 2026-08-07

### Initial Build (Turn 1)

**Agent:** Claude Code (general-purpose)  
**Spec Used:** docs/specs/2026-07-31-Ganoza-eur-receivable-hedge-spec.md (commit be161e6)

**Prompt:**
Read the committed Stage 2 spec at docs/specs/2026-07-31-Ganoza-eur-receivable-hedge-spec.md and build an Excel workbook with all 10 named ranges, money-market 3-step hedge, options, sensitivity ±5%, and validation checks. Save at models/builds/2026-07-31-Ganoza-eur-receivable-hedge-model.xlsx. All formulas must reference named ranges only.

**Agent Output:** Workbook generated successfully with all contract requirements. However, parity check failed: $155,121 difference > $45,000 tolerance.

**Issue Found:** Forward rate `F0_in = 1.0875` violates interest rate parity given R_USD=3.5%, R_FC=1.5%, T=365 days.

### Spec Fix (Turn 1 Audit)

**Diagnosis:** Spec-level defect, not workbook defect. The placeholder forward rate did not obey:
```
F_implied = S0_in × (1 + R_USD × T/360) / (1 + R_FC × T/360)
          = 1.10 × 1.03549 / 1.01519 ≈ 1.1195
```

**Action Taken:** 
- Edited docs/specs/2026-07-31-Ganoza-eur-receivable-hedge-spec.md, §2 table, F0_in row
- Changed F0_in from 1.0875 to 1.1194
- Committed as: 5708405 "Fix Stage-2 spec: update F0_in to parity-compliant value"

### Regeneration (Turn 2)

**Prompt:** Regenerate the workbook using the corrected F0_in = 1.1194. Verify parity check passes.

**Agent Output:** Workbook regenerated.  
Parity check result: $11,571 difference < $45,000 tolerance. ✓ PASS

### Stage 3 Audit (Turn 3)

**Findings Recorded:**
1. Spec defect (forward rate parity) — fixed in spec, regenerated workbook
2. Money-market 3-step formulas — verified correct, all reference named ranges
3. Sensitivity table — verified formula-driven, not hand-typed

**Audit Note:** analysis/2026-07-31-Ganoza-build-audit.md  
**Final Commit:** 15fa031 "Stage 3: Add workbook build and audit artifacts"

**Contract Status:** ✓ All 7 requirements pass

---

End of Stage 3 log.

---

## Stage 4 — Market Data Population (2026-08-07)

Used ECB EUR/USD reference data, FRED DGS1, and the ECB deposit-facility rate to populate the workbook; calculated a CIP-implied forward and reconciled the outputs with the course FX Hedging Lab. Full sources, timestamps, proxy rationale, and reconciliation are recorded in `data/2026-08-07-Ganoza-market-data.md`.

---

## Stage 3 Rebuild — 2026-08-07

**Reason:** The Stage 2 specification was corrected after the initial workbook build. The prior workbook still used `F0_in = 1.1194`, a proceeds-based parity check with a $45,000 tolerance, and an incorrectly labeled receivable-call schedule.

**Rebuild requirements:**

- Use `F0_in = 1.1220`, the rounded result of `F_implied = 1.1219714071`.
- Set `PARITY_CHECK = F0_in − F_implied` and `CHECK_TOLERANCE = 0.0001 USD/EUR`.
- Retain the call only as an EUR-payable reference; exclude it from the receivable recommendation and sensitivity chart.
- Add `USD_NO_HEDGE = FC_AMT × S_T` to the sensitivity table and chart.
- Add `FORW_MM_PROCEEDS_DIFF` as a separate rounding diagnostic.

**Verification result:** The rebuilt workbook passed formula-error scanning and visual inspection. The forward / money-market proceeds difference is $129, consistent with rounding `F0_in` to four decimals; the rate parity difference is 0.0000285929 USD/EUR, inside the 0.0001 tolerance. See `analysis/2026-07-31-Ganoza-build-audit.md` for the complete audit.
