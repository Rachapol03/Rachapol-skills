---
name: spec-to-tasks
description: Synthesize conversation and planning into a Technical Specification and break it down into an actionable Tracer-Bullet Tasks Checklist with dependencies. Trigger on /spec-to-tasks.
disable-model-invocation: true
---

# Spec to Tasks

Bridge the gap between ideation/grilling and hands-on coding. This skill takes the active conversation, settled requirements, and codebase context to produce:
1. **A Technical Specification:** Clear problem statement, proposed architecture seams, explicit boundaries (In-Scope vs Out-of-Scope), and success criteria.
2. **A Tracer-Bullet Tasks Checklist:** Sequential vertical slices with explicit dependency blocking edges, ready for surgical execution.

No additional interview needed; synthesize what has already been decided.

---

## Part 1: Technical Specification

Generate the spec with the following structure (either outputting to the conversation or saving to `SPEC.md` if requested):

```markdown
# Spec: <Feature or Component Name>

## 1. Problem Statement & Intent
- What specific problem is being solved from the user/caller's perspective?
- Why is this being built now?

## 2. Architecture & Seams
- At which seams will this feature be introduced and tested?
- Prefer existing seams over new ones. Keep interfaces narrow and implementations deep.
- Reference relevant terms from `CONTEXT.md` and guidelines from `docs/adr/`.

## 3. Scope Boundaries
- **In-Scope:** Specific capabilities and behaviors included in this iteration.
- **Explicitly Out-of-Scope:** Capabilities, enhancements, or future-proofing explicitly deferred.

## 4. Risks, Assumptions & Trade-offs
- Technical uncertainties, performance constraints, or potential breaking changes.
- Verification criteria: How will we prove this change works?
```

---

## Part 2: Tracer-Bullet Tasks Checklist

Decompose the specification into **Tracer-Bullet Tasks**.

### Slicing Rules

- **Vertical Slices:** Every task cuts a narrow but complete path across all necessary layers (e.g. schema/types → core logic → API/caller → test). Avoid horizontal slicing (e.g., do not write "Task 1: all types, Task 2: all database tables").
- **Independent Verifiability:** Each completed task can be built, tested, and verified on its own.
- **Context-Window Sized:** Each task should comfortably fit within a single agent coding pass.
- **Blocking Edges:** Clearly declare dependencies between tasks.
- **Exception for Wide Refactors (Expand–Contract):**
  If a change has a wide blast radius (e.g. renaming a core symbol across the entire repository), do not force a single vertical slice. Use **Expand–Contract**:
  1. *Expand:* Add the new interface alongside the old.
  2. *Migrate:* Update call sites in batches.
  3. *Contract:* Delete the old interface once all call sites are migrated.

### Checklist Format

```markdown
## Implementation Tasks

- [ ] **Task 1: <Task Title>**
  - **Goal:** <One-sentence objective>
  - **Layers touched:** `<files or modules>`
  - **Blocked by:** None (Ready to start)
  - **Verification:** `<Test command or verifiable check>`

- [ ] **Task 2: <Task Title>**
  - **Goal:** <One-sentence objective>
  - **Layers touched:** `<files or modules>`
  - **Blocked by:** Task 1
  - **Verification:** `<Test command or verifiable check>`

- [ ] **Task 3: <Task Title>**
  - **Goal:** <One-sentence objective>
  - **Layers touched:** `<files or modules>`
  - **Blocked by:** Task 2
  - **Verification:** `<Test command or verifiable check>`
```

---

## Operating Instructions

1. **Review Context:** Read current conversation history, `CONTEXT.md`, and inspect target code files before drafting.
2. **Draft Spec & Checklist:** Generate the complete spec and task breakdown adhering to the templates above.
3. **Validate with User:** Present the breakdown and confirm alignment before proceeding to implementation under `karpathy-guidelines`.
