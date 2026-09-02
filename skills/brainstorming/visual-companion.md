# Visual Companion Guide

Browser-based visual brainstorming companion for mockups, diagrams, options.

## When to Use

Decide per-question, not per-session. Test: **would user understand this better seeing it than reading it?**

**Browser** — content itself is visual: UI mockups (wireframes, layouts, nav structures, component designs), architecture diagrams (components, data flow, relationship maps), side-by-side comparisons (layouts, color schemes, design directions), design polish (look/feel, spacing, hierarchy), spatial relationships (state machines, flowcharts, entity diagrams).

**Terminal** — content is text/tabular: requirements/scope questions, conceptual A/B/C choices, tradeoff lists (pros/cons, comparison tables), technical decisions (API design, data modeling, architecture selection), clarifying questions where answer is words not visual preference.

A question *about* a UI topic isn't automatically visual. "What kind of wizard do you want?" — conceptual, terminal. "Which of these wizard layouts feels right?" — visual, browser.

## How It Works

Server watches a directory for HTML files, serves newest to browser. Write HTML to `screen_dir`; user sees it, clicks to select options. Selections recorded to `state_dir/events`, read on your next turn.

**Fragments vs full documents:** File starting with `<!DOCTYPE` or `<html` served as-is (helper script injected). Otherwise server auto-wraps content in frame template (header, CSS theme, connection status, interactive infra). **Write content fragments by default.** Full documents only when you need complete page control.

## Starting a Session

```bash
# Start AFTER user approves companion. --open auto-opens browser on
# first screen; --project-dir persists mockups and enables same-port restart.
scripts/start-server.sh --project-dir /path/to/project --open

# Returns: {"type":"server-started","port":52341,
#           "url":"http://localhost:52341/?key=ab12…",
#           "screen_dir":"/path/to/project/.superpowers/brainstorm/12345-1706000000/content",
#           "state_dir":"/path/to/project/.superpowers/brainstorm/12345-1706000000/state"}
```

Save `screen_dir`, `state_dir` from response. `--open` opens browser itself on first screen push — still share URL as fallback (headless/remote won't auto-open).

**URL contains session key (`?key=…`).** Server rejects requests without it — always give user the **complete** URL from `url` field, never strip query string, never hand out bare `http://host:port`. Key gates HTTP/WebSocket access so a stray tab or other machine can't read screens or inject events. After first load, browser remembers key via cookie.

**Finding connection info:** Server writes startup JSON to `$STATE_DIR/server-info`. If launched in background and stdout not captured, read that file for URL/port. With `--project-dir`, check `<project>/.superpowers/brainstorm/` for session directory.

Pass project root as `--project-dir` so mockups persist and survive restarts; without it files go to `/tmp` and get cleaned up. Remind user to add `.superpowers/` to `.gitignore`.

**Launching by platform:**

**Claude Code:**
```bash
# Default mode works — script backgrounds server itself.
scripts/start-server.sh --project-dir /path/to/project --open
```
Windows: script auto-switches to foreground mode (blocks tool call). Use `run_in_background: true` on Bash tool call, read `$STATE_DIR/server-info` next turn for URL/port.

**Codex:**
```bash
# Codex reaps background processes. Script auto-detects CODEX_CI and
# switches to foreground mode. Run normally — no extra flags needed.
scripts/start-server.sh --project-dir /path/to/project --open
```

**Gemini CLI:**
```bash
# Use --foreground and set is_background: true on your shell tool call
# so process survives across turns
scripts/start-server.sh --project-dir /path/to/project --open --foreground
```

**Copilot CLI:**
```bash
# Start with Copilot CLI's non-blocking/background shell mechanism so
# server survives across turns. Keep --foreground so harness, not the
# script, owns backgrounding. Launcher is a .sh — invoke via bash
# (on Windows, call Git Bash's bash.exe from the PowerShell tool).
bash scripts/start-server.sh --project-dir /path/to/project --open --foreground
```

**Other environments:** Server must survive across turns. If your environment reaps detached processes, use `--foreground` and launch with your platform's background execution mechanism.

If URL unreachable from browser (remote/containerized), bind non-loopback host:

```bash
scripts/start-server.sh \
  --project-dir /path/to/project \
  --host 0.0.0.0 \
  --url-host localhost
```

`--url-host` controls hostname printed in returned URL JSON.

## The Loop

1. **Check server alive**, write HTML to new file in `screen_dir`:
   - **Required: confirm alive before referring to URL or pushing screen.** Check `$STATE_DIR/server-info` exists and `$STATE_DIR/server-stopped` doesn't. If shut down, restart with `start-server.sh` using **same `--project-dir`** — reuses port, user's tab reconnects on its own ("paused" overlay while down), no new URL needed. Auto-exits after 4hr idle (`--idle-timeout-minutes`).
   - Semantic filenames: `platform.html`, `visual-style.html`, `layout.html`
   - **Never reuse filenames** — fresh file per screen
   - Use file-creation tool — **never cat/heredoc** (dumps noise into terminal)
   - Server auto-serves newest file

2. **Tell user what to expect, end turn:** remind URL (every step), brief summary of what's on screen, ask them to respond in terminal.

3. **Next turn:** read `$STATE_DIR/events` if present (JSON lines of clicks/selections), merge with terminal text. Terminal message is primary; events give structured data.

4. **Iterate or advance:** feedback changing current screen → new file (`layout-v2.html`). Advance only once step validated.

5. **Unload when returning to terminal** — when next step doesn't need browser, push a waiting screen to clear stale content:

   ```html
   <!-- filename: waiting.html (or waiting-2.html, etc.) -->
   <div style="display:flex;align-items:center;justify-content:center;min-height:60vh">
     <p class="subtitle">Continuing in terminal...</p>
   </div>
   ```

6. Repeat until done.

## Writing Content Fragments

Write just the content inside the page — server wraps it in frame template (header, theme CSS, connection status, interactive infra).

```html
<h2>Which layout works better?</h2>
<p class="subtitle">Consider readability and visual hierarchy</p>

<div class="options">
  <div class="option" data-choice="a" onclick="toggleSelect(this)">
    <div class="letter">A</div>
    <div class="content">
      <h3>Single Column</h3>
      <p>Clean, focused reading experience</p>
    </div>
  </div>
  <div class="option" data-choice="b" onclick="toggleSelect(this)">
    <div class="letter">B</div>
    <div class="content">
      <h3>Two Column</h3>
      <p>Sidebar navigation with main content</p>
    </div>
  </div>
</div>
```

No `<html>`, no CSS, no `<script>` needed — server provides it.

## CSS Classes Available

**Options (A/B/C choices):**
```html
<div class="options">
  <div class="option" data-choice="a" onclick="toggleSelect(this)">
    <div class="letter">A</div>
    <div class="content">
      <h3>Title</h3>
      <p>Description</p>
    </div>
  </div>
</div>
```
Multi-select: add `data-multiselect` to container — each click toggles selected styling.
```html
<div class="options" data-multiselect>
  <!-- same option markup — users can select/deselect multiple -->
</div>
```

**Cards (visual designs):**
```html
<div class="cards">
  <div class="card" data-choice="design1" onclick="toggleSelect(this)">
    <div class="card-image"><!-- mockup content --></div>
    <div class="card-body">
      <h3>Name</h3>
      <p>Description</p>
    </div>
  </div>
</div>
```

**Mockup container:**
```html
<div class="mockup">
  <div class="mockup-header">Preview: Dashboard Layout</div>
  <div class="mockup-body"><!-- your mockup HTML --></div>
</div>
```

**Split view (side-by-side):**
```html
<div class="split">
  <div class="mockup"><!-- left --></div>
  <div class="mockup"><!-- right --></div>
</div>
```

**Pros/Cons:**
```html
<div class="pros-cons">
  <div class="pros"><h4>Pros</h4><ul><li>Benefit</li></ul></div>
  <div class="cons"><h4>Cons</h4><ul><li>Drawback</li></ul></div>
</div>
```

**Mock elements (wireframe building blocks):**
```html
<div class="mock-nav">Logo | Home | About | Contact</div>
<div style="display: flex;">
  <div class="mock-sidebar">Navigation</div>
  <div class="mock-content">Main content area</div>
</div>
<button class="mock-button">Action Button</button>
<input class="mock-input" placeholder="Input field">
<div class="placeholder">Placeholder area</div>
```

**Typography:** `h2` page title, `h3` section heading, `.subtitle` secondary text, `.section` content block w/ bottom margin, `.label` small uppercase label.

## Browser Events Format

User clicks recorded to `$STATE_DIR/events` (one JSON object/line), cleared automatically on new screen push.

```jsonl
{"type":"click","choice":"a","text":"Option A - Simple Layout","timestamp":1706000101}
{"type":"click","choice":"c","text":"Option C - Complex Grid","timestamp":1706000108}
{"type":"click","choice":"b","text":"Option B - Hybrid","timestamp":1706000115}
```

Full stream shows exploration path — last `choice` event typically final selection, but click pattern can reveal hesitation worth asking about.

No `$STATE_DIR/events` = user didn't interact with browser — use terminal text only.

## Design Tips

- Scale fidelity to the question — wireframes for layout, polish for polish questions
- Explain the question on each page — "Which layout feels more professional?" not just "Pick one"
- Iterate before advancing — feedback changing current screen → new version
- 2-4 options max per screen
- Use real content when it matters (Unsplash images for a portfolio) — placeholders obscure design issues
- Keep mockups simple — layout/structure over pixel-perfect design

## File Naming

Semantic names (`platform.html`, `layout.html`). Never reuse filenames. Iterations: version suffix (`layout-v2.html`). Server serves newest file by mtime.

## Cleaning Up

```bash
scripts/stop-server.sh $SESSION_DIR
```

`--project-dir` sessions: mockup files persist in `.superpowers/brainstorm/`. Only `/tmp` sessions deleted on stop.

## Reference

- Frame template (CSS reference): `scripts/frame-template.html`
- Helper script (client-side): `scripts/helper.js`
