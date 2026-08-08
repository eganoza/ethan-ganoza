# Stage 2 review — EUR receivable · Treasury sign-off

Ethan — I read your specification the way Treasury actually reads one: the spec is the contract the build must honor, so the test is whether an analyst could hand this to a modeler (or an AI) and get back the workbook you intended, with the exposure pointed the right way. The architecture passes that test. The *numbers you shipped as placeholders* do not, and because your spec says it can be built "without further clarification," that gap is the thing to fix before you build.

| Criterion | Score |
|---|---|
| Named-range contract & tab architecture | 30 / 30 |
| Calculation flow | 30 / 30 |
| Validation & sensitivity plan | 20 / 20 |
| Reproducibility & prompt log | 20 / 20 |
| **Total** | **100 / 100** |

**What you did well — and why it matters**

- **The money-market hedge is built in the correct direction for a receivable.** Borrow EUR sized to `FC_AMT / (1 + R_FC × T_DAYS/360)`, convert at spot, invest USD to settlement — that monetizes the euro you're owed *today* and removes the FX path entirely. Inverting the borrow-side currency is the single most common error on this stage; you didn't make it.
- **The named-range contract is complete and unit-disciplined.** All ten ranges carry a unit, a placeholder, and an explicit "(placeholder)" flag with named Stage-4 sources. That flag is exactly how a desk separates an indicative rate from an executable one, and it's what stops a model from quietly shipping on assumptions nobody re-reviewed.
- **The `ISFORMULA` check is a genuinely good idea.** Requiring a sheet-level test that every summary output is a formula rather than a pasted number is a control most students don't think to specify. It catches the failure mode where someone hardcodes a result during debugging and forgets to restore the link.
- **You correctly noticed the forward and money-market lines don't vary with `S_T`.** Stating that in §6 shows you understand *why* they're flat: both lock at inception. That's the insight the sensitivity chart is supposed to teach.

**Fix these before you build — they will become defects in the workbook**

- **Your placeholder forward contradicts your own interest rates.** With `R_USD = 3.5%` above `R_FC = 1.5%`, covered interest parity puts the EUR forward at a *premium* to spot: `1.10 × 1.0354861 / 1.0152083 = 1.1220`. You shipped `F0_in = 1.0875`, a *discount*. Build it as written and your own parity check fails on day one — `FORW_PROCEEDS` = $4,893,750 against `MM_PROCEEDS` = $5,048,871, a gap of **$155,121 (3.17%)**. Set `F0_in` to the parity value and the gap goes to $0. Direction matters here and is worth internalizing: the higher-rate currency always trades at a forward discount, so USD rates above EUR rates means EUR forward *above* spot.
- **Your parity tolerance is ~1,000× too loose to catch anything.** `CHECK_TOLERANCE = 0.01 × FC_AMT` is $45,000 on a €4.5M notional — about 0.9% of proceeds. A control that permits a $45,000 discrepancy will pass essentially any formula bug you could plausibly write. Tolerance belongs on the *rate*, not the notional: compare `F_implied` to `F0_in` and require agreement within ~$0.0001–0.001 USD per EUR, then let the dollar difference fall out of that.
- **`F_implied` is orphaned.** You define it in §5D and then never use it — `Parity_check` compares proceeds instead. Wire the check to `ABS(F_implied − F0_in) < CHECK_TOLERANCE`. That's the comparison that tells you *which* input is wrong; a proceeds difference only tells you something is.
- **The call payoff is not a call, and a call is not a receivable hedge.** `FC_AMT × MAX(S_T, K_CALL)` with `K_CALL = 1.20` describes proceeds *floored* at 1.20 — that's a put struck at 1.20, not a call. A long EUR call pays `MAX(S_T − K, 0)`, and it hedges a EUR *payable*, not a receivable. Your own §5C flags the ambiguity ("specify the desired payoff") — resolve it in the spec rather than leaving it for the build. Keep the call if you want the comparison, but label it explicitly as a payable-reference schedule that is excluded from the receivable recommendation.
- **The sensitivity table has no unhedged baseline.** You plot forward, MM, put and call, but not `FC_AMT × S_T`. Without it the chart can't answer the CFO's actual question — *what did hedging buy me?* Add `USD_NO_HEDGE(S_T)` as a fifth series; it's the diagonal every other line is judged against.

**To push it further (real-desk nuance)**

- **`K_PUT = 1.00` against spot 1.10 is deeply out-of-the-money — about 9%.** That's a legitimate choice, but be deliberate about it: at a $0.02 premium your floor is `4.5M × 1.00 − 4.5M × 0.02 = $4,410,000`, roughly $484k below what the parity forward would lock. You are accepting a 9% unhedged drawdown before the insurance pays anything. Frame the strike as a risk-tolerance decision — how much floor for how much premium — rather than a default.
- **At Stage 4 the quoted forward won't exactly equal your parity forward.** `F_implied` is a theoretical no-arbitrage rate; a dealer quote embeds a cross-currency basis and a spread. A small gap is basis, not error. A *large* gap means the forward and money-market hedges lock materially different USD amounts — flag the advantaged leg rather than smoothing it away.

**Next — Stage 3**

Hand this spec to the AI to build, then audit what comes back against it. Fix the five items above first — a spec that ships an off-parity forward and a mislabeled call will produce a workbook with exactly those defects, and your Stage 3 audit will be spent rediscovering your own known issues instead of finding new ones. Audit like you expect to find a defect; the spec is tight enough that any surprise is worth understanding.

— Treasury

---

### How to work this review — professional workflow

Treat this PR the way an analyst treats feedback from Treasury — a review is a proposal to engage with, not a checklist to rubber-stamp:

1. **Read it yourself first.** Understand each point and form your own view before changing anything. Disagreeing *with a documented reason* is a legitimate, senior response.
2. **Stress-test it with an LLM (pushback pass).** Paste this review and your spec into your AI assistant and ask it to (a) explain anything you're unsure of more deeply, and (b) argue the *other side* — where might the reviewer be wrong, and what would you give up by making each change. You're building judgment, not just executing edits.
3. **Decide, then draft the changes with the LLM.** For the points you accept, have the AI help implement them — you specify exactly what and why. Your spec is the prompt; precise in, correct out.
4. **Verify — non-negotiable.** Re-run your own checks (the parity tie-out, sensitivity continuity, no error cells) and confirm the numbers before you commit. An AI will hand you a confident wrong edit; verification is what makes the result *yours*.
5. **Close the loop on the PR.** Reply in the thread with what you changed, what you pushed back on and why, then commit and push. Writing down the reasoning is exactly how this works on a real team.

*This is the same human-in-the-loop discipline the whole project is built on: the LLM drafts, you edit and verify, and you own the result.*
