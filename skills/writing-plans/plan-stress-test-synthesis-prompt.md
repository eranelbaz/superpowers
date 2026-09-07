# Plan Stress-Test Synthesis Prompt Template

Dispatch synthesis agent, hardcore mode, after all 3 angle agents return.

**Purpose:** merge 3 angles' findings, one prioritized de-duped list. Returns findings for writing-plans agent to apply — does NOT rewrite plan. Agent holding full plan/spec context owns the edit, same as every other review loop in this repo.

**Dispatch after:** all 3 angle agents done.

```
Subagent (general-purpose):
  model: [standard tier — e.g. anthropic-proxy/claude-sonnet-5]
  description: "Synthesize plan stress-test findings"
  prompt: |
    Three adversarial agents stress-tested a plan, different angles.
    Synthesize into one prioritized, de-duped list. Do NOT rewrite
    plan — findings only.

    **Plan:** [PLAN_FILE_PATH]
    **Spec:** [SPEC_FILE_PATH]

    **Angle 1 ([ANGLE_1]):**
    [paste Angle 1 output]

    **Angle 2 ([ANGLE_2]):**
    [paste Angle 2 output]

    **Angle 3 ([ANGLE_3]):**
    [paste Angle 3 output]

    ## Task

    1. Merge, de-dup overlaps between angles
    2. Conflicts → pick right call, say why
    3. Discard theoretical risk, no practical impact this plan
    4. Order: CRITICAL → IMPORTANT → MINOR

    ## Calibration

    Only real problems — wrong build, stuck implementer, bug a fresh
    dev wouldn't see coming. Style prefs, speculative "what if" below
    MINOR = noise, drop it.

    ## Output

    ## Stress-Test Synthesis

    **Status:** Clean (no Critical/Important) | Issues Found

    **Critical:**
    - [Task X, Step Y]: [problem] — [impact] — [mitigation]

    **Important:**
    - [Task X, Step Y]: [problem] — [impact] — [mitigation]

    **Minor (advisory, doesn't block):**
    - [Task X, Step Y]: [problem] — [mitigation]
```

**Returns:** Status, Critical/Important/Minor findings.
</content>
