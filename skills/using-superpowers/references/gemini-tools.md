# Gemini CLI Tool Mapping

Skills speak in actions ("dispatch a subagent", "create a todo", "read a file"). Gemini CLI equivalents below.

| Action skills request | Gemini CLI equivalent |
|----------------------|----------------------|
| Read a file | `read_file` |
| Read multiple files at once | `read_many_files` |
| Create a new file | `write_file` |
| Edit a file | `replace` |
| Run a shell command | `run_shell_command` |
| Search file contents | `grep_search` |
| Find files by name | `glob` |
| List files and subdirectories | `list_directory` |
| Fetch a URL | `web_fetch` |
| Search the web | `google_web_search` |
| Invoke a skill | `activate_skill` |
| Dispatch a subagent (`Subagent (general-purpose):` template) | `invoke_agent` with `agent_name: "generalist"` (invocable via `@generalist` chat syntax — see [Subagent support](#subagent-support)) |
| Multiple parallel dispatches | Multiple `invoke_agent` calls in the same response |
| Task tracking ("create a todo", "mark complete") | `write_todos` (statuses: pending, in_progress, completed, cancelled, blocked) |

## Instructions file

"Your instructions file" = **`GEMINI.md`**. Loaded hierarchically: global `~/.gemini/GEMINI.md`, project-level files in workspace dirs and ancestors, sub-directory `GEMINI.md` files when tool accesses those dirs.

## Personal skills directory

User-level skills: **`~/.gemini/skills/`**, with **`~/.agents/skills/`** as cross-runtime alias (shared with Codex, Copilot CLI). Both exist at same scope → `.agents/skills/` wins. Each skill = subdirectory with `SKILL.md` (`name`/`description` frontmatter).

## Subagent support

`invoke_agent` (`agent_name`, `prompt` params) dispatches subagents. Shortcut: `@generalist <prompt>` = `invoke_agent` with `agent_name: "generalist"`. Built-in names: `generalist`, `cli_help`, `codebase_investigator`, (browser tooling on) `browser_agent`.

Skills dispatch via `Subagent (general-purpose):`, referencing a prompt-template file (e.g. `superpowers:subagent-driven-development`'s `./implementer-prompt.md`) or inline prompt — all map to: fill template/prompt, then `invoke_agent` with `agent_name: "generalist"` + filled text.

Templates have placeholders (`{WHAT_WAS_IMPLEMENTED}`, `[FULL TEXT of task]`) — fill all before calling `invoke_agent`. Template carries agent's role/criteria/output format; subagent follows it.

Parallel dispatch: multiple `invoke_agent` calls in same response (or multiple `@generalist` in one prompt) for independent work. Keep dependent tasks sequential only.

## Additional Gemini CLI tools

Unique to Gemini CLI:

| Tool | Purpose |
|------|---------|
| `save_memory` (legacy) | Persist facts cross-session when `experimental.memoryV2 = false` |
| `get_internal_docs` | Look up Gemini CLI's bundled docs |
| `ask_user` | Structured user questions (text / single-select / multi-select) |
| `enter_plan_mode` / `exit_plan_mode` | Toggle read-only plan mode |
| `update_topic` | Update conversation's topic / strategic-intent metadata |
| `complete_task` | Gemini subagent signals completion, returns result to parent |
| `tracker_create_task`, `tracker_update_task`, `tracker_get_task`, `tracker_list_tasks`, `tracker_add_dependency`, `tracker_visualize` | Task tracker w/ dependencies + visualization |
| `read_mcp_resource`, `list_mcp_resources` | MCP resource access |
