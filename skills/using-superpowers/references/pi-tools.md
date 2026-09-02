# Pi Tool Mapping

Skills speak in actions ("dispatch a subagent", "create a todo", "read a file"). Pi equivalents below.

| Action skills request | Pi equivalent |
| --- | --- |
| Dispatch a subagent (`Subagent (general-purpose):` template) | Installed subagent tool, e.g. `subagent` from `pi-subagents`, if available |
| Task tracking ("create a todo", "mark complete") | Installed todo/task tool if available, else track in plan or `TODO.md` |

## Subagents

Pi core ships no standard subagent tool. `pi-subagents` (strong optional companion) gives `subagent` tool: single-agent, chain, parallel, async, forked-context, resume/status. None installed? Don't fabricate `Task` calls — execute sequentially in-session or say optional capability missing.

## Task lists

Pi core ships no standard task-list tool. Todo/task extension installed → use its tool. Else use Superpowers plan files, Markdown checklists, or repo-local `TODO.md`. Older `TodoWrite` refs = this action.
