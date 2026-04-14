## Sub-Agents Demo — Follow-Along Cheat Sheet

### What You're Watching

The same RCSA compliance task done two ways:
- **A Side:** One agent does everything in a single prompt
- **B Side:** An orchestrator splits the work and delegates to focused sub-agents

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

**Setup:** 1 Claude chat, all 3 JSON files pasted in, 1 prompt

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

### Step 1: Orchestrator

**Setup:** New Claude chat, all 3 JSON files pasted in

**What it does:**
- Validates inputs
- Builds artifact registry
- Splits work into 4 separate work packages — one per control
- Each package contains ONLY the evidence for that control
- Does NOT write narratives — only delegates

**What to notice:**
- The orchestrator organizes and coordinates — it doesn't do the analysis itself

### Step 2: AC Sub-Agent

**Setup:** New Claude chat, receives ONLY the Access Control work package

**What it does:**
- Writes a 3–5 sentence narrative with inline citations
- Only sees AC evidence — has no idea about CM, DQ, or IH

**What to notice:**
- Tiny, focused context window — just one control's worth of data
- In production, this would run in parallel with the other 3 sub-agents

### Step 3: IH Sub-Agent

**Setup:** New Claude chat, receives ONLY the Incident Handling work package

**What it does:**
- Receives an empty work package (no IH evidence exists)
- Returns: "Gap: No evidence found for Incident Handling."

**What to notice:**
- The sub-agent correctly flagged the Gap instead of making something up
- This failure did NOT affect the AC sub-agent — fault isolation
- CM and DQ sub-agents (not shown live) would also be unaffected

---

## Key Comparison

| | Single Agent | Sub-Agents |
|---|---|---|
| Context window | All data in one window | Each sub-agent gets only its evidence |
| Execution | Sequential | Parallelizable |
| Failure handling | Re-run everything | Isolated to one sub-agent |

---

## Why This Matters

- **Context window conservation** — sub-agents use less memory per task, reducing noise and improving accuracy
- **Parallel execution** — 4 independent sub-agents can run simultaneously instead of one agent working through controls one at a time
- **Fault isolation** — one control failing doesn't crash the pipeline or require re-running the other three

---

## Spec Connections

| What You Saw | Spec Reference |
|-------------|---------------|
| Evidence mapped to controls | FR-016 |
| Narratives with inline citations | FR-017 |
| IH Gap flag | FR-018 |
| Citation validation | FR-019 |
| Never imply compliance without proof | Constitution Principle 1 |
| IH failure didn't break other controls | Constitution Principle 2 |
