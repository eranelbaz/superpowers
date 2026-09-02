# Antigravity CLI (`agy`) Tool Mapping

Skills speak in actions ("dispatch a subagent", "create a todo", "read a file"). Antigravity CLI (`agy`) equivalents below.

| Action skills request | Antigravity CLI equivalent |
|----------------------|----------------------|
| Dispatch a subagent (`Subagent (general-purpose):` template) | `invoke_subagent` with built-in `TypeName` — `self` for full-capability, `research` for read-only |
| Task tracking ("create a todo", "mark complete") | **task artifact** — `write_to_file` with `IsArtifact: true` and `ArtifactType: "task"` (see [Task tracking](#task-tracking)). **Not** `manage_task` (background processes). |

## Task tracking

Antigravity has **no todo tool** (`manage_task` = background process control — `list`/`kill`/`status`/`send_input` — not a checklist). Skill says create todo/track tasks → maintain **task artifact**: markdown checklist via `write_to_file` (`IsArtifact: true`, `ArtifactMetadata.ArtifactType: "task"`), edited via `replace_file_content` / `multi_replace_file_content`.

Multi-step task start → create task artifact listing every plan step. Complete step → mark done (`- [x]`) in artifact. Plan changes → update checklist. Keep current — it's source of truth for what remains; re-read before each step once conversation gets long.
