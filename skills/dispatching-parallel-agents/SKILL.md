---
name: dispatching-parallel-agents
description: Use when facing 2+ independent tasks that can be worked on without shared state or sequential dependencies
---

# Dispatching Parallel Agents

## Overview

Delegate tasks to specialized agents with isolated context. Craft their instructions precisely so they stay focused and succeed. They never inherit your session's context/history — you construct exactly what they need. Preserves your own context for coordination.

Multiple unrelated failures (different files, subsystems, bugs) investigated sequentially wastes time. Independent investigations run in parallel.

**Core principle:** One agent per independent problem domain. Dispatch concurrently.

## When to Use

**Use when:**
- 3+ test files failing with different root causes
- Multiple subsystems broken independently
- Each problem understood without context from others
- No shared state between investigations

**Don't use when:**
- Failures related (fix one might fix others) — investigate together first
- Need full system state understanding
- Agents would interfere (same files, same resources)
- Exploratory debugging — don't yet know what's broken

## The Pattern

### 1. Identify Independent Domains

Group failures by what's broken. Example:
- File A: tool approval flow
- File B: batch completion behavior
- File C: abort functionality

Each domain independent — fixing one doesn't affect others.

### 2. Create Focused Agent Tasks

Each agent gets:
- **Specific scope:** one test file or subsystem
- **Clear goal:** make these tests pass
- **Constraints:** don't touch other code
- **Expected output:** summary of found + fixed

### 3. Dispatch in Parallel

Issue all dispatches in same response — they run concurrently. One per response = sequential, not parallel.

### 4. Review and Integrate

- Read each summary
- Check fixes don't conflict
- Run full test suite
- Integrate changes

## Agent Prompt Structure

Good prompts: **focused** (one problem domain), **self-contained** (all context needed), **specific about output**.

Example: "Fix the 3 failing tests in agent-tool-abort.test.ts: [list tests + symptoms]. Timing/race issue — read test, find root cause, replace arbitrary timeouts with event-based waiting, fix real bugs if found. Do NOT just increase timeouts. Return: summary of found + fixed."

## Common Mistakes

**❌ Too broad:** "Fix all the tests" → agent gets lost
**✅ Specific:** "Fix agent-tool-abort.test.ts" → focused scope

**❌ No context:** "Fix the race condition" → agent doesn't know where
**✅ Context:** paste error messages and test names

**❌ No constraints:** agent might refactor everything
**✅ Constraints:** "Do NOT change production code" / "Fix tests only"

**❌ Vague output:** "Fix it" → you don't know what changed
**✅ Specific:** "Return summary of root cause and changes"

## Real Example

6 failures, 3 files, after major refactoring — independent domains, dispatched one agent per file. Agent 1 replaced timeouts with event-based waiting. Agent 2 fixed event structure bug. Agent 3 added wait for async execution. No conflicts, full suite green.

## Verification

After agents return:
1. Review each summary — understand what changed
2. Check for conflicts — did agents edit same code?
3. Run full suite — verify fixes work together
4. Spot check — agents can make systematic errors
