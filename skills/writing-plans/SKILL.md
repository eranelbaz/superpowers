---
name: writing-plans
description: Use when you have a spec or requirements for a multi-step task, before touching code
---

# Writing Plans

## Overview

Plans target skilled dev, zero codebase context, questionable taste, no toolset/domain knowledge, weak tests. Document all: files/task, code, tests, docs, how to test. Bite-sized. DRY. YAGNI. TDD. Frequent commits.

**Announce:** "I'm using the writing-plans skill to create the implementation plan."

**Context:** work in an isolated worktree — native tool if your harness has one, else `git worktree add <path> -b <branch>` under `.worktrees/`.

**Save to:** `docs/superpowers/plans/YYYY-MM-DD-<feature-name>.md` (user pref wins)

## Scope Check

Multiple independent subsystems in spec? Should've split into sub-project specs at brainstorming. If not, suggest separate plans, one/subsystem, each producing working, testable software alone.

## File Structure

Before tasks, map files to create/modify + each's responsibility — decomposition locks here.

- Clear boundaries, defined interfaces, one responsibility/file.
- Smaller focused files > large — easier context, reliable edits.
- Files changing together live together, split by responsibility not layer.
- Existing codebases: follow patterns, don't restructure unilaterally. Unwieldy file touched anyway → split task fine.

Each task self-contained, standalone change.

## Task Right-Sizing

Task = smallest unit carrying own test cycle, worth fresh reviewer's gate. Fold setup/config/scaffolding/docs into task needing them; split only where reviewer rejects one, approves neighbor. Ends independently testable.

## Bite-Sized Steps

One action/step, 2-5 min: write failing test → run, confirm fail → minimal implementation → run, confirm pass → commit.

## Plan Header

**Must start with:**

```markdown
# [Feature Name] Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** [one sentence]
**Architecture:** [2-3 sentences]
**Tech Stack:** [key technologies/libraries]
**Spec:** [path — travels with plan, executors read both]

## Global Constraints

[Project-wide reqs from spec: version floors, dependency limits, naming/copy rules, platform reqs, one line each, exact values. Every task implicitly includes this.]

---
```

## Task Structure

````markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Interfaces:**
- Consumes: [from earlier tasks, exact signatures]
- Produces: [names/types later tasks rely on; implementer sees only own]

- [ ] **Step 1: Write failing test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

- [ ] **Step 2: Run, verify fails**

Run: `pytest tests/path/test.py::test_name -v` — FAIL "function not defined"

- [ ] **Step 3: Write minimal implementation**

```python
def function(input):
    return expected
```

- [ ] **Step 4: Run, verify passes**

Run: `pytest tests/path/test.py::test_name -v` — PASS

- [ ] **Step 5: Commit**

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```
````

## No Placeholders

Never write:
- "TBD", "TODO", "implement later", "fill in details"
- "Add appropriate error handling" / "add validation" / "handle edge cases"
- "Write tests for the above" (no test code)
- "Similar to Task N" (repeat code, tasks read out of order)
- What without how (code required)
- Types/functions/methods undefined in any task

## Self-Review

Re-check vs spec fresh-eyed, yourself, not subagent.

1. **Coverage:** each requirement has task? List gaps.
2. **Placeholder scan:** check list above.
3. **Type consistency:** names match across tasks? `clearLayers()` Task 3 vs `clearFullLayers()` Task 7 = bug.

Fix inline. Gap → add task.

## Execution Handoff

**"Plan saved to `docs/superpowers/plans/<filename>.md`. Options:**
**1. Subagent-Driven (recommended)** - fresh subagent/task, review between
**2. Inline** - execute this session, batch w/ checkpoints
**Which?"**

Subagent-Driven → **REQUIRED SUB-SKILL:** superpowers:subagent-driven-development
Inline → **REQUIRED SUB-SKILL:** superpowers:executing-plans
</content>
