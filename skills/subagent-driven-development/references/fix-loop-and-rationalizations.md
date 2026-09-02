# Fix Loop Escalation and Breaker

Everything past the two loop exits (minor findings, plan-mandated/conflicting findings — see SKILL.md) enters the loop. One round = one fix dispatch + one scoped re-review, five rounds max per task:

1. **Rounds 1-3, resume original implementer.** Send open findings verbatim, context intact. Can't message a live subagent → fresh implementer carrying brief path, report-file path, findings; report file is persistent memory either way.
2. **Rounds 4-5, fresh implementer + more capable model** (per Model Selection), same paths and findings, framed: "A prior implementer attempted this task [N] times; you own it now. Read the report file for what was tried." Surviving three resumes usually means the implementer can't see its own problem — fresh eyes plus a capability bump fixes that.
3. **Every round:** implementer fixes, re-runs tests covering amended code, appends fix report to same file, returns a short contract. Report must name covering tests, command, output — one-line fix doesn't need the whole suite.
4. **Re-review is scoped.** Run `scripts/review-package PLAN_FILE FIX_BASE HEAD` (FIX_BASE = head previous review saw), dispatch [re-review-prompt.md](../re-review-prompt.md) with findings list, brief, report file, diff path. Reviewer verdicts each finding ADDRESSED/NOT ADDRESSED, flags new breakage in the fix diff only. New Critical/Important breakage joins open findings; out-of-scope observations go to ledger as deferred minors, never extend the loop.
5. **After each round,** append to ledger: `Task <N>: fix round <R>/5 (<X> addressed, <Y> open — <finding one-liners>; commits <a7>..<b7>)`

Never fix findings yourself in the controller session — controller fixes pollute context and skip review.

**The breaker.** Round 5's re-review still leaves findings open → stop dispatching, adjudicate each open finding yourself (you hold plan/cross-task context reviewer lacks):

- **Reviewer wrong or contestable:** park it — `Task <N>: parked — <finding> — Ruling: <why code stands>`. Final review sees both sides.
- **Real, nothing downstream builds on it:** park the same way, ruling notes real and deferred.
- **Real and load-bearing** — later task builds on it, or it reveals a plan defect: rule on the smallest change unblocking dependent work, ledger as `Task <N>: Ruling: <finding> — <what you decided and why>`, carry into next task's dispatch. Parking a structural failure lets every dependent task silently build on it — stop only when the defect leaves every path forward a guess.

Adjudicate only at the cap — earlier is pre-judging with a different name. Every adjudication is a ledger entry; silent discard forbidden.

# Common Rationalizations

| Excuse | Reality |
|--------|---------|
| "Close enough on spec compliance" | Spec gaps = not done. Fix, or hit the cap and adjudicate — only exits. |
| "I'll fix it myself, dispatching is overhead" | Controller fixes pollute context, skip review. Resume the implementer. |
| "One more round will converge" | Past the cap, rounds don't converge — structural failure. Adjudicate and route. |
| "The reviewer will just find something new anyway" | Scoped re-reviews can't wander. New findings on untouched code go to ledger, not the loop. |
| "This finding is obviously wrong, I'll drop it" | Adjudicate only at the cap; every ruling is a ledger entry. Silent discard forbidden. |
| "The fix was small, skip the re-review" | Unreviewed fixes are how regressions land. Every round ends with a scoped re-review. |
| "Reviews slow the loop down" | Loop without reviews is unverified churn. Reviews are the brakes and steering. |
| "Ledger bookkeeping is overhead" | Ledger survives compaction. Controllers without one re-dispatch whole completed sequences. |
| "The implementer spawned its own reviewer — free extra assurance" | Duplicate seat reviewing the same diff. Worker-spawned reviewer is a defect to flag, not rigor. |
