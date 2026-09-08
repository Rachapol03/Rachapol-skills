---
name: grill-with-docs
description: Deep Socratic interview for real projects that sharpens domain models, actively updates CONTEXT.md (glossary), and records architectural decisions into docs/adr/. Trigger on /grill-with-docs.
disable-model-invocation: true
---

# Grill With Docs

A rigorous, Socratic interview process to clarify requirements and architectural design for real software projects. As decisions and terms crystallise, this skill actively updates `CONTEXT.md` (the domain glossary) and records significant architectural choices in `docs/adr/`.

---

## Operating Principles

1. **Facts are the agent's job, decisions are the user's:**
   Never ask the user for facts you can discover yourself. Inspect the filesystem, configuration files, dependencies, code paths, and git history directly. Ask the user exclusively about requirements, business logic, constraints, trade-offs, and design decisions.

2. **Design Tree & Frontier:**
   Structure the discussion as a branching decision tree. Work through the tree in **rounds**. The **frontier** is the set of questions whose prerequisites are already settled.
   - Ask all current frontier questions in a single round.
   - Number each question and provide a sensible recommended answer with rationale.
   - Wait for the user to answer before advancing to the next round.

3. **Capture immediately, don't batch:**
   Update `CONTEXT.md` and create ADRs the moment a term or decision is settled.

---

## Round Format

Present each round cleanly:

```markdown
❓ **Q1** - **<Question Title>**: <Concise question body outlining context and distinct options>

➡️ **Recommendation**: <Recommended option and rationale>

---

❓ **Q2** - **<Question Title>**: <Concise question body outlining context and distinct options>

➡️ **Recommendation**: <Recommended option and rationale>
```

---

## Active Domain Modeling (`CONTEXT.md`)

Actively sharpen domain language throughout the interview:

- **Challenge against the glossary:** When the user uses a term contradicting `CONTEXT.md`, call it out immediately:
  > *"Your glossary defines 'X' as A, but you seem to mean B. Which is it?"*
- **Sharpen fuzzy language:** Propose canonical terms when overloaded or ambiguous words are used (e.g., distinguishing *Customer* vs *User* vs *Account*).
- **Update inline:** Maintain `CONTEXT.md` at the repo root (or per-context according to `CONTEXT-MAP.md`). Refer to [CONTEXT-FORMAT.md](./CONTEXT-FORMAT.md) for strict formatting rules.
- **Glossary only:** `CONTEXT.md` is strictly a dictionary of ubiquitous terms and things to avoid. Do not dump implementation details, specs, or task lists here.

---

## Architectural Decision Records (`docs/adr/`)

Only propose creating an ADR when **all three** criteria are met:
1. **Hard to reverse:** The cost of changing course later is significant.
2. **Surprising without context:** A future maintainer would wonder *"Why was it built this way?"*
3. **Real trade-off:** Genuine alternatives were evaluated and one was chosen for specific reasons.

When all three are satisfied:
- Number sequentially in `docs/adr/NNNN-slug.md` (e.g. `docs/adr/0001-use-postgres.md`).
- Follow the concise format in [ADR-FORMAT.md](./ADR-FORMAT.md) (1-3 sentences stating context, decision, and why).

---

## Workflow Steps

1. **Orientation:** Read existing `CONTEXT.md`, `docs/adr/`, and relevant code paths to establish current project baseline.
2. **Compute Frontier:** Identify missing architectural or domain decisions and form the initial frontier round.
3. **Interview Loop:**
   - Present the current frontier questions with recommendations.
   - Await user input.
   - Update `CONTEXT.md` and/or write an ADR for newly settled items.
   - Recompute the frontier.
4. **Completion:** When the frontier is empty and no ambiguous assumptions remain, summarise the settled architecture and point to the updated docs.
