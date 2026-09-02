## Subagent dispatch requires multi-agent support

Add to `~/.codex/config.toml`:

```toml
[features]
multi_agent = true
```

Enables multi-agent tools for `dispatching-parallel-agents`/`subagent-driven-development`. Tools depend on preset's multi-agent version (current: V2; older: V1). Trust actual tool list over any table, including this one.

- **Spawning:** clean context via `spawn_agent {fork_turns: "none"}`; default `"all"` copies entire transcript. Codex 0.145+: role files under `~/.codex/agents/` attach to isolated forks via `agent_type`. Full-history forks accept `model`/`reasoning_effort` overrides too (only `agent_type` refused) — isolated forks are SDD default for context hygiene, not override restrictions.
- **Fix rounds:** resume implementer with `followup_task` — delivers message, triggers turn, transparently reloads evicted child. Never spawn fresh implementer thinking a spawned agent can't be re-messaged; V2 always can.
- **Lifecycle:** V2 has no `close_agent`; finished children auto-evict, unclosed costs nothing. V1 only: close reviewers on review return, close implementers after review passes.
- **Model names:** never copy a model name into `spawn_agent` without checking current spawn allowlist — V2 hard-errors non-V2-capable presets.

## Waiting on children

`wait_agent` = event subscription, not poll: wakes on mailbox activity regardless of wait length. Short-timeout polling costs a tool call + context rebill for nothing (~2/3 of wait calls in measured sessions were wasted short polls).

- Local work remaining? Don't wait — completed child's answer pushes to mailbox, arrives next turn.
- Genuinely idle with children out → bounded stretches: `wait_agent` `timeout_ms` 300000-600000 (5-10 min). Each stretch end (wake/timeout): one status line, `list_agents`, chase silent finishers. Never poll under 5 min.
- Completion mail can't wake idle controller on its own; `wait_agent` covers that gap. Timeout with no activity → reconcile, don't shorten next stretch.

## Model routing on spawns

Every `spawn_agent` (even from a spawned child fanning out) sets `model` AND `reasoning_effort` explicitly per skill's Model Selection rules. `model` alone silently resets effort to that model's default.

Ask human partner for a config backstop so slipped-through spawns route to a deliberate tier, not priciest model:

```toml
[agents]
default_subagent_model = "<a mid-tier model from your spawn allowlist>"
default_subagent_reasoning_effort = "medium"
```

## Environment Detection

Skills creating worktrees/finishing branches: detect environment with read-only git commands first:

```bash
GIT_DIR=$(cd "$(git rev-parse --git-dir)" 2>/dev/null && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" 2>/dev/null && pwd -P)
BRANCH=$(git branch --show-current)
```

- `GIT_DIR != GIT_COMMON` → already in linked worktree (skip creation)
- `BRANCH` empty → detached HEAD (can't branch/push/PR from sandbox)

Already isolated (`GIT_DIR != GIT_COMMON`) → skip creation. Otherwise native worktree tool if available, else `git worktree add <path> -b <branch>` under `.worktrees/` (verify gitignored first).

## Codex App Finishing

Sandbox blocks branch/push (detached HEAD in externally managed worktree) → commit all work, tell user to use App's native controls:

- **"Create branch"** — names branch, then commit/push/PR via App UI
- **"Hand off to local"** — transfers work to user's local checkout

Agent can still run tests, stage files, output suggested branch names/commit messages/PR descriptions for user to copy.
