## Common Rationalizations for Skipping Testing

| Excuse | Reality |
|--------|---------|
| "Skill is obviously clear" | Clear to you ≠ clear to other agents. Test it. |
| "It's just a reference" | References have gaps too. Test retrieval. |
| "Testing is overkill" | Untested skills have issues. Always. 15 min testing saves hours. |
| "I'll test if problems emerge" | Problems = agents can't use skill. Test BEFORE deploying. |
| "Too tedious to test" | Testing < debugging bad skill in production. |
| "I'm confident it's good" | Overconfidence guarantees issues. Test anyway. |
| "Academic review is enough" | Reading ≠ using. Test application scenarios. |
| "No time to test" | Deploying untested skill wastes more time fixing later. |

**All mean: test before deploying. No exceptions.**

## Bulletproofing Skills Against Rationalization

Discipline skills resist rationalization — agents find loopholes under pressure. Scope: discipline failures only; wrong-shaped output → Match the Form to the Failure instead. Grounded in persuasion research (Cialdini; Meincke et al.: authority, commitment, scarcity, social proof, unity).

**Close every loophole explicitly** — don't just state rule, forbid workarounds. Bad: `Write code before test? Delete it.` Good: `Delete it. Start over. No exceptions: don't keep as "reference", don't "adapt" it while writing tests, don't look at it. Delete means delete.`

**Spirit vs letter** — state early: `**Violating the letter of the rules is violating the spirit of the rules.**` Cuts off "I'm following the spirit" rationalizations. **Build rationalization table** from every excuse in baseline testing (Excuse | Reality). **Red flags list** — bullet every rationalization phrase verbatim, ending "All mean: delete code. Start over." **Update SDO** — add symptoms of ABOUT-to-violate to the description.
