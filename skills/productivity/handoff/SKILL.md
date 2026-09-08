---
name: handoff
description: Compact current conversation state into a structured handoff document saved in the OS temp directory for seamless agent-to-agent session resumption. Trigger on /handoff.
argument-hint: "What will the next session be used for?"
disable-model-invocation: true
---

# Handoff

Compact the current conversation context into a structured handoff document so that a fresh agent in a new session can seamlessly continue the work without token bloat or context degradation.

---

## Core Rules

1. **Save to OS Temp Directory:**
   Always save the generated handoff document to the user's OS temporary directory (never pollute the active workspace or git repository):
   - **Windows:** `$env:TEMP\handoff-<timestamp>.md` or `%TEMP%\handoff-<timestamp>.md`
   - **macOS / Linux:** `/tmp/handoff-<timestamp>.md`
2. **No Duplication:**
   Do not copy-paste code or text already captured in workspace files, specs, ADRs, commits, or PRs. Reference them by relative path or URL.
3. **Auto-Redact Secrets:**
   Strip out API keys, tokens, passwords, private certificates, or personally identifiable information (PII) before writing the document.
4. **Tailor to Next Session:**
   If the user provides an argument (e.g. `/handoff implement user authentication`), tailor the priorities and next steps directly to that focus.

---

## Handoff Document Template

```markdown
# Session Handoff: <Project or Feature Name>
_Generated at: <Timestamp>_

## 1. Goal & Context Summary
- **Current Objective:** <Brief summary of what was being accomplished>
- **Accomplishments in this session:** <Key milestones completed>

## 2. Current State & Artifacts
- **Branch / Commit:** `<git branch / latest commit sha>`
- **Files Modified / Created:**
  - `<path/to/file1>`: <Brief note on changes>
  - `<path/to/file2>`: <Brief note on changes>
- **Key References:**
  - Spec: `<path/to/spec.md>`
  - Domain Glossary: `CONTEXT.md`
  - Relevant ADRs: `docs/adr/<adr-file>.md`

## 3. Immediate Next Steps & Blockers
- [ ] Next action item 1
- [ ] Next action item 2
- **Known Blockers / Caveats:** <Any pitfalls or unsettled issues>

## 4. Suggested Skills for Next Agent
Specify which skills the receiving agent should invoke to continue smoothly:
- `karpathy-guidelines` — for surgical and disciplined implementation
- `scrutinize` — for rigorous review before merging
- `debug-mantra` — if resolving active failures or regressions
```

---

## Workflow Steps

1. **Synthesize:** Review conversation history to extract active goals, decisions made, files touched, and remaining tasks.
2. **Sanitize:** Verify no API keys, credentials, or sensitive secrets appear in the output.
3. **Write to Temp:** Determine the OS temp directory path and write `handoff-<timestamp>.md`.
4. **Report Path:** Present the absolute path of the created handoff document to the user, with instructions on how to load it in the next agent session.