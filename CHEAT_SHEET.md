## Sub-Agents Demo — Follow-Along Cheat Sheet

### What You're Watching

The same RCSA compliance task done two ways:

- **A Side:** One agent does everything in a single prompt
- **B Side:** An orchestrator splits the work and spawns focused sub-agents automatically

Watch for three things:

1. How much data each agent receives (context window)
2. Whether the work is sequential or parallelizable
3. What happens when a control has no evidence

---

## The Task

Turn 3 JSON input files into audit-ready compliance documentation for 4 controls:

| Control | Abbreviation |
|---------|-------------|
| Access Control | AC |
| Change Management | CM |
| Data Quality | DQ |
| Incident Handling | IH |

**The catch:** IH has no evidence in the inputs. The system must detect this and flag it as a Gap — not fake compliance.

---

## A Side — Single Agent

**Setup:** 1 Claude chat (Sonnet), all 3 JSON files pasted in, 1 prompt

**What the agent is asked to do in one shot:**

1. Validate inputs
2. Build artifact registry
3. Map evidence to all 4 controls
4. Write narratives with citations
5. Flag Gaps where evidence is missing
6. Validate citations
7. Produce summary table and validation report

**What to notice:**

- All data lives in one context window
- Processing is sequential — AC, then CM, then DQ, then IH
- If it makes a mistake on one control, the whole pipeline must be re-run

---

## B Side — Orchestrator + Sub-Agents

**Setup:** 1 Claude chat (Sonnet as orchestrator), all 3 JSON files pasted in, 1 prompt — the orchestrator handles everything from here

### Steps 1–2: Validate & Build Registry

**What it does:**

- Validates all 3 JSON inputs are well-formed with expected fields
- Builds an artifact registry mapping each artifact ID to its file path, type, and description

**What to notice:**

- Same validation the single agent did, but now it's a distinct step before any narrative generation

### Step 3: Create Work Packages

**What it does:**

- Splits the evidence into 4 separate work packages — one per control
- Each package contains ONLY the artifacts and tests relevant to that control
- IH work package is explicitly flagged as empty — no matching artifacts or tests

**What to notice:**

- The orchestrator organizes and coordinates — it doesn't do the analysis itself
- AC gets 5 artifacts + 3 tests. CM gets 6 artifacts + 3 tests. DQ gets 2 artifacts + 1 test. IH gets nothing.

### Step 4: Spawn 4 Sub-Agents (Haiku)

**What it does:**

- The orchestrator automatically spawns 4 sub-agents using the Claude Haiku 4.5 model
- Each sub-agent receives ONLY its work package and writes a narrative or flags a Gap
- The presenter does NOT open separate chats — the orchestrator delegates on its own

| Sub-Agent | Control | Input Size | Expected Output |
|-----------|---------|-----------|-----------------|
| Sub-agent 1 | Access Control (AC) | 5 artifacts, 3 tests | Narrative with citations |
| Sub-agent 2 | Change Management (CM) | 6 artifacts, 3 tests | Narrative with citations |
| Sub-agent 3 | Data Quality (DQ) | 2 artifacts, 1 test | Narrative with citations |
| Sub-agent 4 | Incident Handling (IH) | 0 artifacts, 0 tests | Gap flag |

**What to notice:**

- Each sub-agent has a tiny, focused context window — just one control's worth of data
- In production, all 4 sub-agent calls would run in parallel
- Sonnet orchestrates (planning + coordination), Haiku executes (focused narrative generation)

### Steps 5–6: Collect Results & Assemble Output

**What it does:**

- Orchestrator collects all 4 sub-agent results
- Assembles a summary table (Control Name | Status | Citation Count)
- Presents all 4 narratives or Gap statements
- Runs citation validation — confirms every cited artifact ID exists in the registry

**What to notice:**

- IH returned "Gap: No evidence found for Incident Handling." — one line, no commentary
- The IH Gap did NOT affect AC, CM, or DQ — fault isolation
- All citations validated against the artifact registry — zero hallucinated references

---

## Key Comparison

| | Single Agent | Orchestrator + Sub-Agents |
|---|---|---|
| Setup | 1 chat, 1 prompt | 1 chat, 1 prompt (orchestrator spawns sub-agents) |
| Context window | All data in one window | Each sub-agent gets only its control's evidence |
| Execution | Sequential | Parallelizable (4 independent sub-agents) |
| Failure handling | Re-run everything | Isolated to one sub-agent |
| Model usage | Sonnet does everything | Sonnet orchestrates, Haiku executes |

---

## Why This Matters

- **Context window conservation** — sub-agents use less memory per task, reducing noise and improving accuracy. With 13 artifacts it's manageable; with 1,300 in a real enterprise repo, the single agent overflows.

- **Parallel execution** — 4 independent sub-agents can run simultaneously instead of one agent working through controls one at a time. More controls = linear slowdown for single agent, constant time for sub-agents.

- **Fault isolation** — one control failing doesn't crash the pipeline or require re-running the other three. You re-run only the failed sub-agent.

- **Model efficiency** — the powerful model (Sonnet) handles planning and coordination; the lighter model (Haiku) handles scoped execution. Match the model to the task complexity.

---

## Spec Connections

| What You Saw | Spec Reference |
|-------------|---------------|
| Evidence mapped to controls | FR-016 |
| Narratives with inline citations | FR-017 |
| IH Gap flag | FR-018 |
| Citation validation | FR-019 |
| Summary table with Pass/Gap status | FR-020 |
| Never imply compliance without proof | Constitution Principle 1 |
| IH failure didn't break other controls | Constitution Principle 2 |
