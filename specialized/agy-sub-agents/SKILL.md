---
name: agy-sub-agents
description: Orchestrate multi-agent task execution after planning. Decomposes plans via /spec-to-tasks, audits with /scrutinize, and spawns tiered Antigravity sub-agents under /karpathy-guidelines and /debug-mantra. Trigger ONLY on explicit user invocation /agy-sub-agents.
disable-model-invocation: true
---

# Antigravity Sub-Agent Orchestrator (`agy-sub-agents`)

An orchestration workflow that takes a settled plan, decomposes it into verified tasks, and executes them through tiered Antigravity sub-agents without polluting the main orchestrator context.

## Trigger & Operating Stance

- **Trigger:** Explicit user invocation only (`/agy-sub-agents`). The model must never invoke this skill autonomously.
- **Role Separation:** The primary agent conversing with the user serves strictly as the **Orchestrator**.
  - **Zero Direct Coding:** The Orchestrator does not write, edit, or refactor code files.
  - **No Self-Assignment:** The Orchestrator never acts as an execution worker.
  - **Coordination Only:** Evaluates task complexity, spawns sub-agents, monitors progress, handles dependencies, and conducts final reviews.

---

## Complete Workflow Pipeline

```
[Initial Plan / Blueprint]
            │
            ▼
   1. /spec-to-tasks   ◄────────────────────────┐
            │                                   │
            ▼                                   │ (Needs revision)
   2. /scrutinize  ───► [Plan Audit Passed?] ───┘
            │ Yes
            ▼
   3. Task Evaluation & Thinking Profile Assignment
      (Inherit Model + Low / Medium / High Reasoning Depth)
            │
            ▼
   4. Sub-Agent Spawning (invoke_subagent)
      ├── Coding Constitution: /karpathy-guidelines
      └── Incident Response:   /debug-mantra
            │
            ▼
   5. Final Verification (/scrutinize)
            │
            ▼
     [Job Complete]
```

---

## Phase 1: Plan Decomposition (`/spec-to-tasks`)

Once an initial plan or architecture has been formulated:

1. Ingest the plan, requirements, and codebase context.
2. Execute the decomposition protocol defined in `/spec-to-tasks`.
3. Generate:
   - **Technical Specification:** Clear problem statement, architecture seams, scope boundaries (in-scope vs explicitly out-of-scope), and risks.
   - **Tracer-Bullet Tasks Checklist:** Sequential vertical slices where each task cuts across all necessary layers (types -> logic -> caller -> test) with explicit dependency blocking edges.

---

## Phase 2: Plan Scrutiny & Refinement Loop (`/scrutinize`)

Before spawning any sub-agents, stress-test the decomposed tasks using `/scrutinize`:

1. Read the plan and task checklist cold from an outsider perspective.
2. Verify:
   - **Intent:** Is there a simpler, smaller, or more elegant approach that accomplishes the exact same goal?
   - **Seams & Assumptions:** Does the plan assume system states or dependencies that do not exist?
   - **Slice Granularity:** Can each task be independently verified within a single sub-agent execution pass?
3. **Decision Gate:**
   - **If revisions are required:** Refine the technical plan to resolve the critique, then re-run `/spec-to-tasks` to update the task checklist. Repeat until the plan passes audit cleanly.
   - **If approved:** Proceed to task assignment and sub-agent dispatch.

---

## Phase 3: Task Evaluation & Thinking Profile Assignment

The Orchestrator assesses each task in the checklist to determine its cognitive difficulty and assigns an appropriate **Thinking Profile** (e.g. Low, Medium, High reasoning depth under the active Gemini 3.8 Flash model):

- **Base Model Configuration:** When calling `invoke_subagent`, the `Model` parameter is set to `'inherit'`. This ensures all sub-agents run on the exact same model family as the Orchestrator (e.g. Gemini 3.8 Flash) rather than switching between physically different model families.
- **Cognitive Depth Control:** Because Antigravity's `invoke_subagent` tool uses the `'inherit'` enum for model parity, the cognitive budget (Low vs Medium vs High) is enforced directly via the sub-agent prompt instructions.

| Task Difficulty | Cognitive Profile | Thinking Guidance in Prompt | Example Tasks |
| :--- | :--- | :--- | :--- |
| **Low** | **Fast / Minimal Reasoning** (Gemini 3.8 Flash Low) | Minimal reasoning budget. Immediate, direct, and surgical actions. Do not over-analyze or generate extensive architectural thoughts. | Renaming files or directories, updating documentation, simple string replacements, basic boilerplate scaffolding. |
| **Medium** | **Balanced Reasoning** (Gemini 3.8 Flash Medium) | Balanced reasoning budget. Plan the narrow seam, check dependencies, and verify with tests before finishing. | Implementing single-module features, writing unit tests for existing handlers, wiring API routes, routine bug fixes. |
| **High** | **Deep Reasoning** (Gemini 3.8 Flash High) | Maximum reasoning budget. Deep edge-case exploration, architectural seam tracing, strict hypothesis falsification before modifying code. | Core algorithmic engines, cross-cutting refactors (Expand-Contract), race condition diagnosis, multi-service synchronization. |

---

## Phase 4: Sub-Agent Spawning & Supervision

### Sub-Agent Execution Rules

1. **Coding Discipline:** Every sub-agent performing code modifications must strictly follow the `/karpathy-guidelines` skill:
   - Think before coding (state assumptions, avoid silent choices).
   - Simplicity first (write the absolute minimum code, no speculative abstractions).
   - Surgical changes (touch only what is necessary, avoid touching adjacent code or pre-existing formatting).
   - Goal-driven execution (write and run tests to verify success criteria).
2. **Bug Resolution Discipline:** If any error, unexpected test failure, or regression arises during implementation, the sub-agent must invoke `/debug-mantra` immediately:
   - Reproduce reliably before modifying code.
   - Know the fail path (debugger -> source trace -> probe instrumentation).
   - Falsify hypotheses before applying fixes.
   - Keep a breadcrumb ledger of experiments.
   - Never apply speculative blind fixes.

### Dispatching Sub-Agents

When calling `invoke_subagent`:

- **Required Parameters:** Always provide `TypeName: 'self'`, `Role`, `Prompt`, and `Model: 'inherit'`. Using `TypeName: 'self'` ensures the sub-agent inherits tools to create files, edit files, and run commands.
- **Model Consistency:** Set `Model: 'inherit'` to preserve the Orchestrator's Gemini 3.8 Flash model.
- **Dependency & Concurrency Rules (Disjoint Boundaries):**
  - **Sequential by Default:** Tasks with dependency blocking edges or tasks that touch overlapping files (shared interfaces, index files, config files) MUST run sequentially.
  - **Strict Scope Partitioning for Parallel Dispatch:** Independent tasks may ONLY run in parallel if the Orchestrator explicitly assigns disjoint, mutually exclusive file boundaries (e.g. Worker A touches only `src/auth/*` and Worker B touches only `src/billing/*` with zero file overlap).
  - **No Concurrent Git Mutations:** Sub-agents running in parallel must not execute global git mutating commands (such as `git commit`, `git checkout`, or `git rebase`). All commits are handled sequentially or staged after workers finish.
- **Cognitive Profile Injection:** Inject the assigned Thinking Profile (Low / Medium / High) clearly into each worker prompt.

#### Sub-Agent Prompt Template

```markdown
Role: [e.g., Worker Agent: Directory Structure Migration]
TypeName: self
Model: inherit

Task: [Exact task description from the tracer-bullet checklist]

Thinking Profile: [Low (Minimal / Fast) | Medium (Balanced) | High (Deep Reasoning)]
- Reasoning Guidance: [e.g. For Low: Execute directly with minimal internal reasoning. Focus on immediate surgical edits.]

Target File Boundaries (Strict Scope Partitioning):
- Allowed Touched Files: [Explicit list or glob of files this worker may create or modify]
- Prohibited Files: [Explicitly state any shared files off-limits to prevent race conditions]

Context & Constraints:
- Dependencies: [Completed work from earlier tasks, relevant types/contracts]

Mandatory Execution Guidelines:
1. Follow /karpathy-guidelines strictly:
   - Make surgical edits only within your allowed file boundaries.
   - Keep implementations minimal and avoid unnecessary abstractions.
   - Verify changes with runnable tests or commands.
2. If any bug, failure, or unexpected behavior occurs:
   - Apply /debug-mantra immediately.
   - Formulate a reproducible test case and falsify root causes before touching code.
3. Report back upon completion with:
   - Summary of files modified or created.
   - Verification evidence (command output or test pass logs).
   - Any notes or interface exports needed by subsequent tasks.
```

### Worker Output Verification & Milestone Tracking

Upon receiving completion messages from sub-agents:

1. **Verify Evidence:** Inspect the reported test pass logs or command outputs. Do not mark a task complete on mere claims without evidence.
2. **Boundary Check:** Confirm that touched files remained strictly within the worker's assigned `Allowed Touched Files`.
3. **Compact User Updates:** Post a concise milestone update to the user (e.g. `[Milestone] Task 2/5 (Auth Service) completed with verified tests. Dispatching Task 3...`). Never copy raw worker transcripts into the main chat to preserve the orchestrator context window.

---

## Phase 5: Final Review & Job Closeout (`/scrutinize`)

Once all sub-agents report successful task completion:

1. Do not declare victory immediately.
2. Execute `/scrutinize` over the collective diff and implementation:
   - Trace the actual execution paths end-to-end through the modified code.
   - Verify that all claims and requirements from the original specification are satisfied.
   - Ensure no unintended side effects, dead code, or orphan exports remain.
3. **Audit & Report Gate (Human-in-the-Loop):**
   - **No Automated Remediation Loop:** To prevent infinite AI loops and uncontrolled token consumption, the Orchestrator does NOT automatically dispatch remediation sub-agents upon finding issues.
   - **Verdict Reporting:**
     - If `/scrutinize` passes: Present the complete summary of delivered capabilities, sub-agent runs, and test verifications to the user.
     - If `/scrutinize` identifies flaws or blockers: Present the findings, evidence, and suggested changes directly to the user. Await explicit human direction before taking any further action.
