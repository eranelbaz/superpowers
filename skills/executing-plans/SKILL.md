---
name: executing-plans
description: Use when you have a written implementation plan to execute in a separate session with review checkpoints
---

# Executing Plans

## Overview

Load plan, review critically, execute all tasks, report when complete.

**Announce at start:** "I'm using the executing-plans skill to implement this plan."

**Note:** tell human partner Superpowers works better with subagents (Claude Code, Codex CLI, Codex App, Copilot CLI, Gemini CLI all qualify; see per-platform tool refs in `../using-superpowers/references/`). Subagents available → use superpowers:subagent-driven-development instead.

## The Process

### Step 1: Load and Review Plan
1. Ensure isolated workspace: native worktree tool if available, else `git worktree add <path> -b <branch>` under `.worktrees/`. Skip if already isolated (`git rev-parse --git-dir` != `--git-common-dir`).
2. Read plan file
3. Review critically — note questions/concerns
4. Concerns → raise with human partner before starting
5. No concerns → create todos for plan items, proceed

### Step 2: Execute Tasks

Per task: mark in_progress → follow each step exactly (bite-sized) → run verifications as specified → mark completed.

### Step 3: Complete Development

All tasks done + verified:
- Run full test suite. Failing → stop, report, fix before continuing.
- Passing → present options: 1) merge to base locally 2) push + open PR 3) keep branch as-is. Wait for the answer — don't decide for them.
- After merge or explicit discard: clean up worktree (`git worktree remove`), delete branch.

## When to Stop and Ask for Help

**STOP immediately when:**
- Blocker hit (missing dependency, test fails, instruction unclear)
- Plan has critical gaps preventing start
- Instruction not understood
- Verification fails repeatedly

**Ask for clarification, don't guess.**

## When to Revisit Earlier Steps

**Return to Step 1 when:**
- Partner updates plan from feedback
- Fundamental approach needs rethinking

**Don't force through blockers** - stop and ask.

## Remember
- Review plan critically first
- Follow steps exactly, don't skip verifications
- Reference skills when plan says to
- Stop when blocked, don't guess
- Never implement on main/master without explicit user consent
