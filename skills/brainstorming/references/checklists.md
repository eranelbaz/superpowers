# Brainstorming — Full Checklists & Process Flow

Detailed reference for the Architectural path and the full process
flow diagram. Spike and Bounded checklists stay inline in SKILL.md —
they're short and used on nearly every request. Load this file when
running the Architectural path or when the flow diagram helps clarify
branching/terminal states.

## Architectural Checklist (Full)

| # | Step |
|---|------|
| 1 | Explore context, files, docs, recent commits |
| 2 | Offer visual companion just-in-time (own message, only when clearer shown) — approval opens browser tab, see Visual Companion in SKILL.md |
| 3 | Ask clarifying questions one at a time: purpose, constraints, success criteria; multiple choice preferred |
| 4 | Propose 2-3 approaches, trade-offs + recommendation, leading with it; YAGNI ruthlessly |
| 5 | Present design in sections scaled to complexity (sentences to 200-300 words), approval after each: architecture, components, data flow, errors, testing |
| 6 | Write design doc to `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md` (user location overrides), commit |
| 7 | Spec self-review inline: placeholders/TBD, contradictions, scope, ambiguity (pick one reading, make explicit); fix inline, no re-review |
| 8 | User reviews written spec — "Spec written and committed to `<path>`. Review and flag changes before implementation plan." Wait; changes → fix step 7 |
| 9 | Invoke writing-plans skill, no other skill |

## Process Flow

```dot
digraph brainstorming {
    Classify [shape=diamond];
    "Probe (2-3 sentences)" [shape=box];
    "Clarify (bounded)" [shape=box];
    "Short design in chat" [shape=box];
    Approves [shape=diamond, label="Approves?"];
    "Investigate; report" [shape=doublecircle];
    "Implement (no plan doc)" [shape=doublecircle];
    "Explore context" [shape=box];
    "Clarify" [shape=box];
    "Propose approaches" [shape=box];
    "Design sections" [shape=box];
    "Design approved?" [shape=diamond];
    "Write spec" [shape=box];
    "Self-review (fix inline)" [shape=box];
    "Spec approved?" [shape=diamond];
    "writing-plans skill" [shape=doublecircle];
    "Hidden complexity" [shape=box];

    Classify -> "Probe (2-3 sentences)" [label="spike"];
    Classify -> "Clarify (bounded)" [label="bounded"];
    Classify -> "Explore context" [label="architectural"];
    "Probe (2-3 sentences)" -> Approves;
    "Clarify (bounded)" -> "Short design in chat";
    "Short design in chat" -> Approves;
    Approves -> "Investigate; report" [label="spike: yes"];
    Approves -> "Implement (no plan doc)" [label="bounded: yes"];
    "Hidden complexity" -> Classify;
    "Explore context" -> Clarify;
    Clarify -> "Propose approaches";
    "Propose approaches" -> "Design sections";
    "Design sections" -> "Design approved?";
    "Design approved?" -> "Design sections" [label="no"];
    "Design approved?" -> "Write spec" [label="yes"];
    "Write spec" -> "Self-review (fix inline)";
    "Self-review (fix inline)" -> "Spec approved?";
    "Spec approved?" -> "Write spec" [label="changes"];
    "Spec approved?" -> "writing-plans skill" [label="yes"];
}
```

Terminal states path-bound: architectural ends at writing-plans only;
bounded ends at normal dev workflow; spike ends at reported
recommendation.
