---
name: subagent-driven-development
description: Use when executing implementation plans with independent tasks in the current session
---

# Subagent-Driven Development

Core principle: fresh implementer subagent per task, task review (spec + quality) after each, broad whole-branch review at end. Isolated context, no inherited session history — preserves your context for coordination.

**Narration:** at most one short line between tool calls — ledger and results carry the record.

**Continuous execution:** run all tasks without stopping. Only the four things below, or all-complete, stop you. "Should I continue?" and progress summaries waste your human partner's time.

**Rulings, not stalls.** A running plan doesn't wait on a human. Conflicts, ambiguities, plan defects, a cap you'd exceed — decide them. Spec is binding authority, plan its argument, judgment settles the rest. Record `Ruling: <what you decided> — <why> — <what it costs if wrong>`, keep going. Wrong ruling = visible, undoable rework; parked session = whole day lost, nothing gained.

**Only four things stop you:** irreversible/destructive operation, security-sensitive action, a side effect this worktree's norms say ask first about (merge, push shared branch, publish), or a plan so broken every path is a guess.

## When to Use

Need: implementation plan, mostly-independent tasks, staying in this session. Missing plan or tightly coupled tasks → manual execution or brainstorm first. Independent tasks but a parallel session → executing-plans instead.

**vs. Executing Plans:** same session, no context switch, no human-in-loop between tasks; fresh subagent per task avoids pollution; review after each task plus broad review at end.

## The Process

Per task: dispatch implementer → questions? answer, else work → implements/tests/commits/self-reviews → review package + task reviewer → spec✅+quality approved? ledger complete, next task. Else: plan conflict? rule + ledger → fix round R of 5 (R≤3 resume implementer, R≥4 fresh implementer + stronger model) → scoped re-review → addressed? ledger complete. Not yet, R<5: next round. R=5: adjudicate each finding — load-bearing? rule and continue (stop only if every path is a guess); else park with ruling → ledger complete either way → more tasks? loop, else final code reviewer → findings? one fix dispatch, one scoped re-review, adjudicate residuals → clean: delete workspace → run tests, present merge/PR/keep options, wait.

## Setup

Never start on main/master without your human partner's explicit consent.

Memory doesn't survive compaction — track progress in a ledger, not only todos. Lost-place controllers re-dispatch entire completed task sequences, the costliest failure observed.

- Run `scripts/sdd-workspace PLAN_FILE` at skill start — creates/prints the plan's git-ignored workspace dir (ledger, briefs, reports, review packages; rationale in script comments). Never touch another plan's dir.
- Ledger lives at `<workspace>/progress.md`, named for your plan. `Task <N>: complete` lines → resume at first task without one; last line a fix round → resume at next round. Different plan named, or stray ledger at old flat path `.superpowers/sdd/progress.md` → leave it, start fresh with `# SDD ledger — plan: <plan file path>` as first line.
- Ledger is the recovery map: trust it + `git log` over recollection after compaction. `git clean -fdx` destroys the workspace, not git history.

Read plan once, note context and Global Constraints, create a todo per task. Plan names a Spec → read it, spec is binding authority. No reachable spec → ledger note; rulings without one are provisional.

Before Task 1, scan once for conflicts, logging what you checked: tasks contradicting each other or Global Constraints; anything plan mandates that the review rubric treats as a defect (test asserting nothing, verbatim duplication). Output a table, not a verdict — one row per task-pair sharing a file/interface (produces vs consumes, what found), one row per task (self-consistent — tests vs code, files vs later touches). "Scan is clean" without those rows isn't a scan you ran. Write it to ledger, rule on everything found before dispatching Task 1; clean scan needs no comment. Review loop only nets conflicts emerging from implementation.

## Model Selection

Least powerful model per role — saves cost, adds speed. Always specify explicitly; omitted model inherits session's (often most capable/expensive), silently defeating this section.

- **Mechanical** (isolated functions, clear specs, 1-2 files, complete plan text): fast/cheap — most tasks, when plan is well-specified.
- **Integration/judgment** (multi-file coordination, pattern matching, debugging): standard.
- **Architecture/design, and final whole-branch review**: most capable.
- **Review**: scaled to diff size/complexity/risk — small mechanical diff ≠ top model, subtle concurrency change does. Scoped re-reviews of small fix diffs: cheap-to-mid.
- **Fix-loop escalation (rounds 4-5)**: at least one tier above the stuck implementer.

**Turn count beats token price.** Cheapest models take 2-3× the turns on multi-step work, costing more overall — mid-tier is the floor for reviewers/implementers working from prose. Exception: plan text with complete code to write is transcription+testing — cheapest tier fine, same for single-file mechanical fixes.

## The Task Loop

**Batch small same-shape work.** Several small, independent same-kind edits (one-line fix, constant change, field addition across files): one dispatch brief listing every file/change, one subagent, one diff review — not one subagent per task. One-dispatch-per-task is for work needing its own judgment, tests, or review surface.

Dispatch prompts and subagent replies stay resident in context, re-read every turn — hand artifacts over as files.

**Waiting on subagents:** never poll short timeouts, never sit in one silent open-ended wait. Local work available (ledger, packaging next review, reading reports): keep working. Genuinely idle: bounded 5-10 min stretches, one status line between, reconcile live children (chase any finished-without-reporting).

### 1. Dispatch the implementer

Record BASE (`git rev-parse HEAD`) before dispatching — review package and fix-round diffs need it.

- **Task brief:** run `scripts/task-brief PLAN_FILE N` — extracts task's full text to a uniquely named file, prints path; single source of requirements. Dispatch contains: (1) one line where task fits in project; (2) brief path, framed "read this first — your requirements, exact values verbatim"; (3) interfaces/decisions from earlier tasks brief can't know; (4) your resolution of ambiguity in the brief; (5) report-file path + report contract. Exact values (numbers, magic strings, signatures, test cases) go in the brief only — never a whole plan file for a subagent to read.
- **Report file:** name after brief (`…/task-N-brief.md` → `…/task-N-report.md`). Implementer writes full report there, returns only status, commits, one-line test summary, concerns.
- Dispatch describes one task, not session history — don't paste prior-task summaries (one real dispatch hit 42k chars, 99% pasted history). Fresh subagent needs task, touched interfaces, global constraints, nothing else.
- No-subagents contract: implementer template forbids dispatching subagents, helpers, or a reviewer — review comes from you after the report (worker-spawned reviewers duplicated task review anyway).
- Earlier task parked a finding in this task's area → carry a pointer to that ledger entry.
- Record implementer's agent identity — fix rounds 1-3 resume it. Never dispatch multiple implementation subagents in parallel (conflicts).

Template: [implementer-prompt.md](implementer-prompt.md)

### 2. Handle the report

Implementer subagents report one of four statuses:

**DONE:** Generate review package: `scripts/review-package PLAN_FILE BASE HEAD` (BASE = commit recorded before dispatch — never `HEAD~1`, which silently drops all but the last commit of a multi-commit task), dispatch task reviewer with the printed path.

**DONE_WITH_CONCERNS:** Doubts flagged, read first. Correctness/scope: address before review. Observations ("file getting large"): note, proceed.

**NEEDS_CONTEXT:** Missing info — provide, re-dispatch.

**BLOCKED:** Assess: (1) context problem → provide it, re-dispatch same model; (2) needs more reasoning → re-dispatch more capable model; (3) task too large → break into pieces; (4) plan itself wrong → rule on correction, ledger it, re-dispatch with ruling carried. Never ignore an escalation or force the same model to retry unchanged — stuck means something must change.

Implementer questions get clear, complete answers and extra context before it resumes implementation.

### 3. Review the task

Per-task reviews are task-scoped gates; broad review happens once, at the end. Never skip task review or accept a report missing either verdict — spec compliance AND task quality both required. Self-review never replaces task review.

- Hand reviewer its diff as a file: run `scripts/review-package PLAN_FILE BASE HEAD` (no-bash fallback documented in the script), pass the printed path. Reviewer reads commit list, stat summary, full diff in one Read call — output never enters your context. Use the recorded BASE, never `HEAD~1`. Never dispatch without a diff file.
- **Reviewer inputs:** three paths — brief, report, review package — plus global constraints binding the task.
- Global-constraints block is the reviewer's attention lens: copy binding requirements verbatim from plan's Global Constraints or spec (exact values, formats, stated relationships like "same layout as X"). Reviewer template already carries process rules (YAGNI, test hygiene, review method) — this block is only for what THIS project's spec demands.
- Don't add open-ended directives ("check all uses", "run race tests if useful") without a concrete, task-specific reason.
- Don't ask reviewer to re-run tests implementer already ran — report carries test evidence.
- Don't pre-judge findings, never instruct reviewer to ignore or downgrade an issue — looks like a false positive? Let reviewer raise it, adjudicate in the loop. Prompt containing "do not flag," "don't treat X as a defect," "at most Minor," "the plan chose" — stop: pre-judging.

Reviewer may report "⚠️ Cannot verify from diff" items (requirements in unchanged code or spanning tasks). Don't block the rest of review — resolve each yourself before marking task complete (you hold plan/cross-task context reviewer lacks). Confirmed real gap → failed spec review, enters fix loop.

Template: [task-reviewer-prompt.md](task-reviewer-prompt.md)

### 4. The fix loop

Loop triggers on review spec ❌, any Critical/Important finding, or a ⚠️ item confirmed as a real gap.

Two exits before the loop starts:

- **Minor findings:** log in ledger as you go (`Task <N>: minor (deferred): <one-liner>`), point final whole-branch review at that list. Roll-up nobody reads = silent discard. Minors never enter the loop.
- **Plan-mandated or plan-conflicting findings:** yours to rule on — weigh against plan text, decide with spec as binding authority, ledger the ruling before acting. Don't dismiss because plan mandates it; don't fix against the plan without a recorded ruling.

Everything else enters the loop: max 5 rounds per task, one fix dispatch + one scoped re-review per round, escalating model/implementer at round 4, adjudicated by a breaker at round 5's cap if findings remain open. Full round-by-round mechanics, escalation rules, and breaker adjudication logic: [references/fix-loop-and-rationalizations.md](references/fix-loop-and-rationalizations.md).

Never fix findings yourself in the controller session — controller fixes pollute context and skip review.

### 5. Complete the task

Review clean, or every open finding parked with a ruling at the cap: append a completion line to ledger in the same message as other bookkeeping.

- `Task <N>: complete (commits <base7>..<head7>, review clean)`
- `Task <N>: complete (commits <base7>..<head7>, <K> parked)` after a tripped breaker

Then mark todo complete, move on. Never move to next task with open Critical/Important issues neither fixed nor parked-with-ruling at the cap.

## Final Review

Run `scripts/review-package PLAN_FILE MERGE_BASE HEAD` (MERGE_BASE = commit branch started from, e.g. `git merge-base main HEAD`), include the printed path in final dispatch — reviewer reads one file, not a re-derived branch diff. Dispatch on most capable model as a general-purpose subagent: give it the diff, plan, and ledger's deferred-minor/parked lines; ask for merge-readiness verdict (Critical/Important/Minor issues, ready to merge Y/N) covering plan alignment, code quality, architecture, tests, production readiness.

Findings returned → dispatch ONE fix subagent with the complete findings list, not one fixer per finding (per-finding fixers rebuild context and re-run suites each time; one real session's final-review fix wave cost more than all its tasks combined). Run exactly one scoped re-review of the fix wave (`scripts/review-package PLAN_FILE FIX_BASE HEAD` over fix range, [re-review-prompt.md](re-review-prompt.md)). Adjudicate residual findings as in the task loop's breaker: park with rulings, or rule on load-bearing ones and ledger the decision. Only the four stop-classes above stop you here — no second fix wave, residual load-bearing findings surface to your human partner in the merge/PR/keep menu at Finish.

## Finish

Before deleting anything, collect every ledger line containing `Ruling:` — preflight rulings, parked findings, breaker adjudications — into final message under "Rulings I made", in order made, each with what it costs if wrong. Exhaustive: ledger holds a ruling, list holds it. A ruling dying with the workspace is a decision made in secret.

Final review clean, fixes merged → delete this plan's workspace (`rm -rf <workspace>`) — git history is the record now. Sibling directories belong to other plans; leave them alone.

Run full test suite (stop and fix on failure), then present: 1) merge to base locally 2) push + open PR 3) keep branch as-is. Wait for the answer.

## Common Rationalizations

Excuses for skipping the fix loop, ledger, or reviews, and why each one is wrong: [references/fix-loop-and-rationalizations.md](references/fix-loop-and-rationalizations.md).
