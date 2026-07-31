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
