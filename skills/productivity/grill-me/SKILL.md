---
name: grill-me
description: Relentless Socratic interview to sharpen a plan, decision, or architecture idea in-chat without modifying or creating files. Trigger on /grill-me or when you need to stress-test your thinking before coding.
disable-model-invocation: true
---

# Grill Me

A relentless Socratic interview to stress-test ideas, plans, decisions, or system designs purely within the conversation. It operates **statelessly**—sharpening your thinking without creating or editing files in the repository.

---

## Operating Principles

1. **Facts are the agent's job, decisions are the user's:**
   If a question relies on facts already present in the workspace, environment, or configuration, inspect them directly (or dispatch a subagent). Never ask the user for information you can discover yourself. Only interview the user on decisions, intents, constraints, and preferences.

2. **Design Tree & Frontier:**
   Model the problem as a branching design tree. Every decision exposes new subordinate choices. Work through the tree in **rounds**. The **frontier** consists of all decisions whose prerequisites have been settled.
   - Present all frontier questions for the current round at once.
   - For every question, propose a clear recommended choice with rationales.
   - Await the user's answers before generating the next round.

3. **Stateless in-chat focus:**
   Do not generate `CONTEXT.md` or ADR files. Keep the entire synthesis within the conversation. (If you want to produce domain glossary and architectural decision records directly in the codebase, use `/grill-with-docs` instead.)

---

## Round Format

Structure each question round with crisp, scannable formatting:

```markdown
❓ **Q1** - **<Question Title>**: <Question description, context, and distinct options>

➡️ **Recommendation**: <Your recommended answer and rationale>

---

❓ **Q2** - **<Question Title>**: <Question description, context, and distinct options>

➡️ **Recommendation**: <Your recommended answer and rationale>
```

---

## Workflow Steps

1. **Analyze Subject:** Parse the user's stated plan, idea, or challenge. Inspect relevant environment files if needed to understand the background.
2. **Compute Initial Frontier:** Formulate the initial set of pivotal questions and recommendations.
3. **Iterate Rounds:**
   - Present the frontier questions.
   - Process user choices.
   - Unblock downstream questions and recompute the frontier.
4. **Wrap Up:**
   When the frontier is empty and no critical assumptions remain untested, present a cohesive summary of the refined plan.