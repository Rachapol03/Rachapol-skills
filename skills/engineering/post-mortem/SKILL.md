---
name: post-mortem
description: Write the canonical engineering record of a fixed bug — root cause, mechanism, fix, validation, and prevention. Engineer-to-engineer, code identifiers welcome. Use after a debug session lands a fix, before closing out work. Trigger on /post-mortem, when the user says "write the post-mortem / postmortem / RCA / root cause analysis", "document this fix", or after resolving an issue with debug-mantra.
---

# Post-mortem

The canonical engineering record of a bug fix. Written **after** debugging lands a real fix, **for** future-you (who will have forgotten everything in 6 months) and anyone else touching this code.

Code identifiers are first-class here — this is the artifact that lets someone recover the mental model fast without re-deriving the bug.

---

## When to Invoke

- Explicit `/post-mortem` invocation.
- User requests: "write the post-mortem / RCA / root cause analysis", "document this fix", "write up the root cause", or "close out this bug with a writeup".
- After a `debug-mantra` session has clearly landed and validated a fix, proactively offer: *"Would you like me to draft a post-mortem for this fix?"*

## When NOT to Use

- **Bug not fixed yet, or fix not validated.** A post-mortem of an unproven hypothesis is misleading. Stop and state what is missing.
- **Trivial fix** (typo, obvious one-liner with no non-obvious cause). Don't manufacture ceremony for simple fixes.

---

## Required Inputs — Do Not Draft Without These

Before writing a single line, confirm all four items are satisfied:

- [ ] **1. Reliable repro exists** (a deterministic test, script, or verifiable steps).
- [ ] **2. Root cause is known** (the exact code mechanism is identified, not a guess).
- [ ] **3. Fix is identified** (specific code changes, commit, or branch).
- [ ] **4. Fix is validated** (the repro now passes; the workload or test succeeds).

> **Integration with `debug-mantra`:**  
> These map directly to steps 1–4 of `debug-mantra`. Pull raw material directly from the breadcrumb ledger and the failing test from that session.

---

## Document Structure (5 High-Impact Sections)

Save the output to `docs/post-mortems/<YYYY-MM-DD>-<slug>.md` (create directory lazily if needed).

```markdown
# Post-Mortem: <Descriptive Title of the Bug>
_Date: <YYYY-MM-DD> | Component/Area: <affected-module>_

### 1. Summary & Symptom
One concise paragraph. What broke in user/system terms, and what was the observable symptom (actual error message, test failure, or log line).

### 2. Root Cause
The actual bug mechanism in the code. Name every code identifier explicitly — function names, file paths, variables, branch conditions, or offending commit SHAs. Walk the cause chain from trigger to failure.

### 3. The Fix
What changed and **why this change fixes the root cause** rather than merely masking the symptom. Reference the modified files or commit. If an earlier naive attempt was rejected, state why.

### 4. Validation
Concrete proof that the fix works:
- Name of the failing test that now passes.
- Repro workload result (before vs after).
- State tested configurations honestly (e.g. *"Validated on Node v20 / Windows; not tested on Linux"*).

### 5. Prevention & Regression Test
How we ensure this bug never returns:
- Name and path of the permanent regression test added.
- Architectural invariant or guardrail established to prevent this class of bug.
```

---

## Tone & Writing Rules

- **Code identifiers are mandatory:** Use exact names (`src/services/cart.ts::applyVoucher`, `timeoutMs`, `HTTP 409`). Future-you will grep for them.
- **Mechanism over narrative:** Describe how the data flowed and where the logic broke, not vague stories like "the service experienced instability".
- **No hedging:** Drop words like "we believe", "probably", "might have". State observed facts.
- **Blameless:** Describe system gaps, logic errors, and concurrency races — never assign blame to individuals.

---

## Output Workflow

1. **Verify inputs:** Confirm repro, root cause, fix, and validation are ready.
2. **Draft & Save:** Create `docs/post-mortems/<YYYY-MM-DD>-<slug>.md` with the 5 sections.
3. **Report:** Display the completed post-mortem in chat and provide the file path for reference.