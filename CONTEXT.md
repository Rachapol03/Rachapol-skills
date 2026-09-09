# Rachapol Skills

A curated ecosystem of open agent skills compliant with [skills.sh](https://skills.sh) and loaded by AI coding agents (Claude Code, Antigravity, Cursor, Codex). Skills provide predictable, surgical workflows for engineering and productivity tasks.

## Language

**Skill**:
A directory containing a `SKILL.md` instruction file with YAML frontmatter (`name`, `description`, optional `disable-model-invocation`), defining instructions and workflows for an AI agent.
_Avoid_: prompt file, agent plugin, instruction snippet

**Bucket**:
A primary organizing directory under `skills/`: `engineering/` (code-centric tools) or `productivity/` (ideation, domain modeling, session handoffs, and token efficiency).
_Avoid_: category folder, skill group

**User-invoked skill**:
A skill marked with `disable-model-invocation: true`. It strips description matching from autonomous model reach and is invoked exclusively by a human slash-command in the chat.
_Avoid_: manual skill, human-only macro

**Model-invoked skill**:
A skill omitting `disable-model-invocation`. The model continuously matches context against its frontmatter description and autonomously activates it when appropriate.
_Avoid_: auto-skill, background trigger

**Socratic Grilling**:
A structured, round-based interview discipline (`grill-me` or `grill-with-docs`) that traverses a branching design tree to uncover implicit assumptions and lock in design constraints before coding.
_Avoid_: brainstorming chat, prompt interrogation

**Frontier**:
In a grilling session, the set of all decision nodes whose prerequisites are already settled and can be presented in the current round.

**Tracer-bullet task**:
A vertical development slice produced by `spec-to-tasks` that cuts across all necessary layers (types, logic, APIs, tests), independently verifiable and sized to fit a single context window.
_Avoid_: subtask, ticket chunk

**Surgical change**:
The core discipline of modifying only the lines necessary to satisfy a task, leaving adjacent code, comments, formatting, and unrelated dead code untouched.

**Post-mortem**:
The canonical, blameless engineering record of a resolved bug capturing root cause, code mechanisms, validation, and prevention.
_Avoid_: bug recap, incident summary

**Handoff**:
A sanitized markdown summary written to the OS temp directory allowing a fresh agent session to continue in-flight work with zero context bloat.

**Caveman Mode**:
An ultra-compressed communication protocol (`caveman`) that trims conversational pleasantries, articles, and prose filler while strictly preserving technical substance, code, and errors.
_Avoid_: brief mode, short chat

**Conventional Commit**:
A standardized git commit format (`git-commit`) derived by analyzing real diffs to determine type, scope, and semantic description.
_Avoid_: git message, changelog note

**Skill Discovery**:
The process of searching, evaluating (installs, security, reputation), and adding agent capabilities from the open ecosystem using `find-skills`.
_Avoid_: plugin search, add-on store

## Relationships

- A **Bucket** contains multiple **Skills**
- A **Skill** is either **User-invoked** or **Model-invoked**
- `grill-with-docs` produces and sharpens `CONTEXT.md` and `docs/adr/`
- `spec-to-tasks` consumes the interview output to emit **Tracer-bullet tasks**
- `debug-mantra` isolates bugs and feeds its breadcrumb ledger to `post-mortem`
- `handoff` captures current state to bridge across session boundaries
- `caveman` minimizes token burn and latency during active collaboration without altering persisted code or documentation
- `git-commit` inspects verified changes and commits them with semantic conventional messaging
- `find-skills` extends agent capabilities dynamically by discovering and installing skills from skills.sh
