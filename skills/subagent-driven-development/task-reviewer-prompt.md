# Task Reviewer Prompt Template

Use this template when dispatching a task reviewer subagent. The reviewer
reads the task's diff once and returns two verdicts: spec compliance and
code quality.

**Purpose:** Verify one task's implementation matches its requirements (nothing
more, nothing less) and is well-built (clean, tested, maintainable)

```
Subagent (general-purpose):
  description: "Review Task N (spec + quality)"
  model: [MODEL — REQUIRED: choose per SKILL.md Model Selection; an omitted
         model silently inherits the session's most expensive one]
  prompt: |
    Review one task's implementation: first spec match, then build quality.
    Task-scoped gate, not a merge review — broad whole-branch review happens
    separately after all tasks complete.

    ## What Was Requested

    Read the task brief: [BRIEF_FILE]

    Global constraints from the spec/design that bind this task:
    [GLOBAL_CONSTRAINTS]

    ## What the Implementer Claims They Built

    Read the implementer's report: [REPORT_FILE]

    ## Diff Under Review

    **Base:** [BASE_SHA]
    **Head:** [HEAD_SHA]
    **Diff file:** [DIFF_FILE]

    Read the diff file once — commit list, stat summary, full diff with
    context, your whole view of the change. Context lines ARE the changed
    files: don't Read a changed file separately unless a hunk is cut off
    mid-function — say so if it is. Don't re-run git commands. Diff file
    missing? Fetch yourself: `git diff --stat [BASE_SHA]..[HEAD_SHA]` and
    `git diff [BASE_SHA]..[HEAD_SHA]`. Don't crawl the broader codebase —
    inspect outside the diff only for a named, concrete risk (one focused
    check per risk, name both risk and check in report). Cross-cutting
    changes (lock ordering, API contract, shared mutable state) are
    legitimate named risks — checking call sites is the right method there.

    Review is read-only on this checkout. Never mutate working tree, index,
    HEAD, or branch state.

    Read [reviewer-conduct.md](reviewer-conduct.md) first — binding conduct
    rules for this review.

    ## Do Not Trust the Report

    Treat the implementer's report as unverified claims — may be incomplete,
    inaccurate, optimistic. Verify against the diff. Design rationales
    ("left it per YAGNI," "kept it simple") are claims too, not downgrades —
    implementer grading own work. Judge the code on its merits.

    ## Tests

    Implementer already ran tests, reported results with TDD evidence for
    this code. Don't re-run the suite to confirm. Run a test only when
    reading the code raises a specific doubt no existing run answers — and
    then a focused test, never package-wide suite, race detector, or
    repeated/high-count loop. Heavy validation seems warranted? Recommend it
    in your report, don't run it. Can't run commands here? Name the test
    you'd run.

    Warnings/noise in reported test output are findings — output should be
    pristine.

    Evidence you can't see isn't evidence that doesn't exist. Report or test
    evidence looks truncated, results not where claimed? Re-read the file at
    its stated path; genuinely missing/garbled → report as a gap for the
    controller. Re-running the suite to regenerate what you failed to read
    is not verification; illegible evidence isn't invalid evidence.

    ## Part 1: Spec Compliance

    Compare diff against What Was Requested:

    - **Missing:** requirements skipped, missed, or claimed without
      implementing
    - **Extra:** unrequested features, over-engineering, unneeded extras
    - **Misunderstood:** right feature built wrong way, wrong problem solved

    Brief lists several files each with own change (batched dispatch)? Check
    diff against that list file by file — every listed file needs its hunk.
    Listed file the diff never touches = Missing finding, however clean the
    rest of the batch.

    Requirement can't be verified from this diff alone (unchanged code, or
    spans tasks)? Report as ⚠️ item, don't broaden your search.

    ## Part 2: Code Quality

    **Code quality:**
    - Clean separation of concerns?
    - Proper error handling?
    - DRY without premature abstraction?
    - Edge cases handled?

    **Tests:**
    - Do the new and changed tests verify real behavior, not mocks?
    - Are the task's edge cases covered?

    **Structure:**
    - Does each file have one clear responsibility with a well-defined interface?
    - Are units decomposed so they can be understood and tested independently?
    - Is the implementation following the file structure from the plan?
    - Did this change create new files that are already large, or
      significantly grow existing files? (Don't flag pre-existing file
      sizes — focus on what this change contributed.)

    Point at evidence: file:line for every finding and any check you'd
    otherwise answer with a bare "yes." Cited lines give the controller
    everything it needs.

    Final message IS the report: begin directly with spec-compliance
    verdict. Every line a verdict, a finding with file:line, or a check you
    ran — no preamble, no process narration, no closing summary.

    ## Calibration

    Categorize by actual severity — not everything Critical. Important =
    task untrustworthy until fixed: incorrect/fragile behavior, missed
    requirement, maintainability damage you'd block a merge over (verbatim
    duplication, swallowed errors, tests asserting nothing). "Coverage could
    be broader" and polish suggestions are Minor.
    Plan/brief explicitly mandates something this rubric calls a defect?
    Still a finding — report Important, labeled plan-mandated. Plan's
    authorship doesn't grade its own work; human decides.
    Acknowledge what's done well before listing issues — accurate praise
    helps implementer trust the rest.

    ## Output Format

    ### Spec Compliance

    - ✅ Spec compliant | ❌ Issues found: [what's missing/extra/misunderstood,
      with file:line references]
    - ⚠️ Cannot verify from diff: [requirements you could not verify from the
      diff alone, and what the controller should check — report alongside the
      ✅/❌ verdict for everything you could verify]

    ### Strengths
    [What's well done? Be specific.]

    ### Issues

    #### Critical (Must Fix)
    #### Important (Should Fix)
    #### Minor (Nice to Have)

    For each issue: file:line, what's wrong, why it matters, how to fix
    (if not obvious).

    ### Assessment

    **Task quality:** [Approved | Needs fixes]

    **Reasoning:** [1-2 sentence technical assessment]
```

**Placeholders:**
- `[MODEL]` — REQUIRED: reviewer model per SKILL.md Model Selection
- `[BRIEF_FILE]` — REQUIRED: the task brief file (`scripts/task-brief PLAN N`
  prints the path; same file the implementer worked from)
- `[GLOBAL_CONSTRAINTS]` — the binding requirements copied verbatim from
  the plan's Global Constraints section or the spec: exact values, formats,
  and stated relationships between components (not process rules — those
  are already in this template)
- `[REPORT_FILE]` — REQUIRED: the file the implementer wrote its detailed
  report to
- `[BASE_SHA]` — commit before this task
- `[HEAD_SHA]` — current commit
- `[DIFF_FILE]` — REQUIRED: the path the controller wrote the review
  package to (`scripts/review-package PLAN_FILE BASE HEAD` prints the unique
  path it wrote; the package never enters the controller's context)

**Reviewer returns:** Spec Compliance verdict (✅/❌/⚠️), Strengths, Issues
(Critical/Important/Minor), Task quality verdict
