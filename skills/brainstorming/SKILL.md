---
name: brainstorming
description: "You MUST use this before any creative work - creating features, building components, adding functionality, or modifying behavior. Explores user intent, requirements and design before implementation."
---

# Brainstorming Ideas Into Designs

Turn ideas into designs/specs via dialogue. Classify how much process
the request needs, work the path: context, refine idea, present
design, get approval.

<HARD-GATE>
Do NOT invoke any implementation skill, write code, scaffold a
project, or take implementation action until you've told your human
partner your intent and they approved. EVERY task, EVERY path —
ceremony scales with task; approval gate never does.
</HARD-GATE>

## Three Paths

Before first question, classify request, say it out loud ("this looks
bounded") so human partner can override:

- **Spike** — feasibility question ("can we...", "is it possible...",
  "quick and dirty is fine"). Output an answer, not kept code. No
  design doc, no spec file. Anything built stays labeled throwaway.
- **Bounded** — well-scoped change to code already in this repo: new
  flag, small endpoint, one-file fix. Requires the flow you're
  changing already exists to read, not just app-type familiarity —
  no existing flow = not bounded. Design IN CHAT, no spec file, no
  plan doc. Approval as hard a gate as architectural.
- **Architectural** — new projects/subsystems, changes restructuring
  how components fit or altering interfaces others depend on. Full
  process: questions, approaches, sectioned design, written spec,
  writing-plans skill.

When in doubt, take the heavier path. Ratchet one-way: hidden
complexity mid-task upgrades the path — stop, say so, step up, never
downgrade.

Even "simple" tasks need a two-sentence chat design and a yes.
Artifact scales with simplicity, approval never does.

## Red Flags

| Thought | Reality |
|---------|---------|
| "Too simple for a design" | Short design, not no design. Two sentences, approval. |
| "Call it bounded, skip the spec" | Label to dodge work IS the doubt — heavier path. |
| "Bounded and obvious — start while they read" | Gate is approval, not design length. Present, stop for yes. |
| "I understand this app type, so bounded" | Bounded measures repo, not familiarity. New = no flow = architectural. |
| "Spike works, keep the code" | Spike output is an answer. Keeping code = new request — classify it. |
| "It grew, but almost done" | Complexity upgrades path mid-task. Stop, say so. |
| "Spike approved, so follow-up is too" | Each task, own classification, approval. |

## Checklist

Classify first, announce path, complete steps in order.

**Spike**

| # | Step |
|---|------|
| 1 | Explore context, enough to frame the probe |
| 2 | Present question + probe plan (2-3 sentences) |
| 3 | Get approval — a nod is enough |
| 4 | Investigate, cheaply as correctness allows |
| 5 | Report findings + recommendation; label built stuff throwaway |

**Bounded**

| # | Step |
|---|------|
| 1 | Explore context, files, docs, recent commits |
| 2 | Ask clarifying questions one at a time, only ones that matter |
| 3 | Present short design in chat: approach, files touched, testing |
| 4 | Get approval — STOP, explicit yes; presenting+starting same breath skips gate |
| 5 | Implement via normal dev workflow (failing test first, watch fail, implement); no plan doc |

**Architectural** (summary — full detail incl. what each step covers: `references/checklists.md`)

| # | Step |
|---|------|
| 1 | Explore context |
| 2 | Offer visual companion just-in-time |
| 3 | Clarifying questions one at a time |
| 4 | Propose 2-3 approaches + recommendation |
| 5 | Present design in sections, approval after each |
| 6 | Write design doc, commit |
| 7 | Spec self-review inline, fix |
| 8 | User reviews written spec, wait |
| 9 | Invoke writing-plans skill |

Multiple independent subsystems in one request ("chat, storage,
billing, analytics") — flag immediately, decompose into sub-projects,
brainstorm the first; each gets own spec → plan → implementation cycle.

Design for isolation: one purpose per unit, clear interfaces, testable
independently — large file signals too much. Follow existing patterns;
fold targeted improvements in, no unrelated refactoring.

## Process Flow

Full dot graph of state transitions: `references/checklists.md`.

Terminal states path-bound: architectural ends at writing-plans only;
bounded ends at normal dev workflow; spike ends at reported
recommendation.

## Visual Companion

Browser tool for mockups, diagrams, visual options. A tool, not a
mode: available when useful, not every question through browser.

**Offering (just-in-time):** don't offer upfront. Wait until a
question is genuinely clearer shown than told — real
mockup/layout/diagram, not merely a UI *topic*. First time, own
message, nothing else:
> "This next part might be easier if I show you — I can put together mockups, diagrams, and comparisons in a browser tab as we go. It's still new and can be token-intensive. Want me to? I'll open it for you."

Accepted → start server with `--open`, browser opens automatically.
Declined → stay text-only, don't offer again unless raised.

**Per-question decision:** browser or terminal, per-question not
per-session — full criteria and examples in
`visual-companion.md` ("When to Use").

If accepted, read: `skills/brainstorming/visual-companion.md`
