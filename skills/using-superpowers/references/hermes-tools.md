# Hermes Agent Tool Mapping

Skills speak in actions ("dispatch a subagent", "create a todo", "read a file"). Hermes Agent tools below.

## Tools

| Action skills request | Hermes tool |
|---|---|
| Read a file | `read_file` |
| Create a new file | `write_file` |
| Edit a file (targeted patch) | `patch` |
| Run a shell command | `terminal` |
| Search file contents | `search_files` |
| Find files by name | `terminal` with `find` |
| Fetch a URL / read a webpage | `web_extract(urls=[...])` |
| Search the web | `web_search(query=...)` |
| Dispatch a subagent | `delegate_task(goal=..., context=..., toolsets=[...], role="leaf")` |
| Task tracking | `todo` tool |
| Invoke a skill | `skill_view("skill-name")` |

## Instructions file

"Your instructions file" = **`AGENTS.md`** (project dir) or **`SOUL.md`** (global, `~/.hermes/SOUL.md`).

## Invoking a skill

`skills` toolset has `skill_view` and `skills_list`. To invoke:

```
skill_view("brainstorming")
skill_view("superpowers:brainstorming")
```

`skill_view` misses a superpowers skill (may not be in catalog until plugin fully registers)? Fall back:

```
read_file(path="~/.hermes/plugins/superpowers/skills/<skill-name>/SKILL.md")
```

Same fallback other no-native-skill-loading harnesses use.

## Subagent dispatch

`delegate_task` spawns isolated subagents, parallel or sequential:

```
delegate_task(goal="...", context="...", toolsets=[...], role="leaf")
```

Unavailable? Work inline, don't invent tool calls.

## Task tracking

`todo` tool for in-session tracking. Multi-agent boards: `hermes kanban` CLI if available. Older `TodoWrite` refs = this action.
