---
name: systematic-debugging
description: Use when encountering any bug, test failure, or unexpected behavior, before proposing fixes
---

# Systematic Debugging

**Core principle:** ALWAYS find root cause before fixing. Symptom fixes = failure.

```
NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST
```

## When to Use

Any technical issue: test failures, bugs, unexpected behavior, perf problems, build/integration failures. Especially under time pressure, "quick fix" temptation, repeat failures, partial understanding. "Simple" bugs have root causes too.

## The Four Phases

Complete each before next.

### Phase 1: Root Cause Investigation

1. **Read errors carefully** — stack traces, line numbers, file paths, codes usually hold the answer.
2. **Reproduce consistently** — exact steps, every time. Can't reproduce → gather more data, don't guess.
3. **Check recent changes** — git diff, commits, new deps, config/env changes.
4. **Multi-component systems** (CI→build→signing, API→service→DB): log data in/out at each boundary, verify env/config propagation, check state per layer. Run once, find where it breaks:

   ```bash
   echo "IDENTITY: ${IDENTITY:+SET}${IDENTITY:-UNSET}"   # workflow
   env | grep IDENTITY || echo "not in environment"        # build script
   security list-keychains; security find-identity -v      # signing script
   codesign --sign "$IDENTITY" --verbose=4 "$APP"          # actual signing
   ```
5. **Trace data flow backward** — from symptom to where bad value first enters. Fix there, not at symptom site.

### Phase 2: Pattern Analysis

1. **Find working examples** — similar working code in same codebase.
2. **Compare against references** — read reference impl completely, every line.
3. **Identify differences** — list every one, however small; don't assume any can't matter.
4. **Understand dependencies** — which components/settings/config/env/assumptions are needed.

### Phase 3: Hypothesis and Testing

1. **Form single hypothesis** — "I think X is root cause because Y." Specific, not vague.
2. **Test minimally** — smallest change, one variable at a time.
3. **Verify before continuing** — worked → Phase 4; didn't → new hypothesis, don't stack fixes.
4. **Don't know** — say so. Ask, research more.

### Phase 4: Implementation

1. **Write failing test first** — simplest reproduction, watch it fail, must exist before fixing.
2. **Implement single fix** — root cause only, ONE change. No "while I'm here" extras, no bundled refactors.
3. **Verify fix** — test passes, no other tests broken, issue resolved. Run the actual command, read full output — never claim success from memory or assumption.
4. **Add validation** at each layer the bad value passed through (defense in depth).
5. **Replace arbitrary sleeps/timeouts** with poll-until-condition.
6. **Fix doesn't work** — STOP, count attempts. <3: back to Phase 1 with new info. ≥3: question architecture, don't attempt fix #4 blind.

**3+ fixes failed → question architecture, not "try again."** Signs: each fix reveals new shared state/coupling, needs "massive refactoring," spawns new symptoms. Sound pattern, or inertia? Discuss with human partner — wrong architecture, not a failed hypothesis.

## Red Flags — STOP, Return to Phase 1

- "Quick fix for now, investigate later"
- "Just try changing X and see if it works"
- "Add multiple changes, run tests"
- "Skip the test, I'll manually verify"
- "It's probably X, let me fix that"
- "Don't fully understand but this might work"
- "Pattern says X but I'll adapt it differently"
- "Here are the main problems" (lists fixes without investigation)
- Proposing solutions before tracing data flow
- "One more fix attempt" (already tried 2+)
- Each fix reveals new problem elsewhere

**Human partner signals same thing:** "Is that not happening?" (assumed, didn't verify) · "Will it show us...?" (should've gathered evidence) · "Stop guessing" · "Ultra-think this" (question fundamentals) · "We're stuck?" (approach failing) → STOP, return to Phase 1.

## Common Rationalizations

| Excuse | Reality |
|--------|---------|
| "Simple issue, don't need process" | Process is fast for simple bugs too. |
| "Emergency, no time for process" | Systematic beats guess-and-check thrashing. |
| "Just try this first, then investigate" | First fix sets the pattern — do it right from start. |
| "I'll write test after confirming fix works" | Untested fixes don't stick; test first proves it. |
| "Multiple fixes at once saves time" | Can't isolate what worked; causes new bugs. |
| "Reference too long, I'll adapt the pattern" | Partial understanding guarantees bugs — read it fully. |
| "I see the problem, let me fix it" | Seeing symptoms ≠ understanding root cause. |
| "One more fix attempt" (after 2+ failures) | 3+ failures = architectural problem. |

## When Process Reveals "No Root Cause"

Truly environmental, timing-dependent, or external: process complete — document investigation, implement handling (retry, timeout, error message), add monitoring.

**But:** 95% of "no root cause" cases are incomplete investigation.
