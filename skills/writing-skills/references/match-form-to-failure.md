# Match the Form to the Failure

Classify baseline failure before writing guidance — form that bulletproofs one failure type backfires on another.

| Baseline failure | Right form | Wrong form |
|---|---|---|
| Skips/violates rule under pressure (knows better, does it anyway) | Prohibition + rationalization table + red flags (see Bulletproofing below) | Soft guidance ("prefer...", "consider...") |
| Complies, but output wrong shape (bloated prompt, buried verdict, restated spec) | Positive recipe/contract: state what output IS — parts, in order | Prohibition list ("don't restate", "never narrate") |
| Omits required element from something already produced | Structural: REQUIRED field/slot in the template | Prose reminders near template |
| Behavior should depend on condition | Conditional keyed to observable predicate ("if brief exists, reference it") | Unconditional rule + exemption clauses |

**Why prohibitions backfire on shaping problems:** under competing incentive, agents negotiate with "don't X". Wording tests on dispatch-prompt guidance: prohibition arm produced more unwanted content, trended worse than no-guidance control. Micro-test, don't default to prohibition.

**No nuance clauses** — "don't X unless it matters" reopens negotiation, degraded a winning recipe to noisy in testing; express exceptions as own conditional instead. **Exemption clauses don't scope** — "doesn't apply to code blocks" still suppresses code blocks; restructure so rule can't reach the exempt part.

**Testing discipline:** test before deploying, no exceptions — see [rationalization table](rationalization-and-bulletproofing.md) for excuses vs reality. **Bulletproofing discipline skills:** close every loophole explicitly, don't just state the rule — forbid workarounds, cut off "spirit not letter" rationalizations, build rationalization table + red flags list from baseline testing. Full guidance: [rationalization-and-bulletproofing.md](rationalization-and-bulletproofing.md).
