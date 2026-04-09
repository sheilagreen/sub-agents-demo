# Building Your Analytical Team in VS Code — Complete Cheat Sheet

> **Purpose:** This guide walks you through building a team of specialized AI sub-agents using GitHub Copilot in VS Code. By the end, you'll have a "Team Leader" agent that delegates tasks to specialist agents automatically.

---

## Prerequisites

Before starting, make sure you have the following:

- [ ] Visual Studio Code (latest version)
- [ ] GitHub Copilot Chat extension installed (check for updates — you may need the pre-release version)
- [ ] Active GitHub Copilot subscription (Pro or Business tier recommended)
- [ ] Signed into GitHub within VS Code
- [ ] **This repo cloned and opened in VS Code** (`File → Open Folder`)

### What's already in this folder

| File | Purpose |
|------|---------|
| `sales_q1.csv` | Messy demo data for the agents to clean and visualize |
| `CHEAT_SHEET.md` | This file — your step-by-step guide |

> 💡 Open `sales_q1.csv` and take a look. Notice the inconsistent date formats, the missing revenue value, and the NULL entry. By the end of this demo, your AI team will fix all of that automatically.

---

## Step 1: Create the Team Leader Agent File

1. Press `Ctrl+Shift+P` (Windows) or `⌘+Shift+P` (Mac) to open the **Command Palette**
2. Type **Chat: New Custom Agent** and select it
3. When prompted "Select a location to create the agent file," choose **.github/agents**
4. When prompted "Enter the name of the agent file," type **AnalyticsLeader** and press Enter
5. VS Code will create and open the file `AnalyticsLeader.agent.md`

### What to put in the file

Delete any auto-generated template content. Replace the **entire** file with the following:

```yaml
---
name: AnalyticsLeader
description: I manage your data projects and delegate to specialists.
tools: ['agent', 'read', 'search']
agents:
  - DataCleaner
  - ChartExpert
---
```

```markdown
# Analytics Team Leader

You are an expert project manager for data analytics.

When a user asks for help:

1. Ask the DataCleaner to check the spreadsheets for errors.
2. Ask the ChartExpert to propose the best way to visualize the data.

Always check the "Blueprint" (SPEC.md) before starting.
```

> ⚠️ **Important:** The YAML block (between the `---` fences) and the system prompt (below the closing `---`) should all be in the same file, one after the other. The code blocks above are separated here for readability only.

### Watch out for these gotchas

| Gotcha | What to do |
|--------|-----------|
| Copilot autocomplete suggests a complex nested format with `name:`, `description:`, `tools:` for each agent | **Press Escape** to dismiss it. Use the simple list format above. |
| You see grayed-out "Configure Tools..." text | That's a VS Code UI overlay — it's NOT in your file. Ignore it. |
| The `---` fences | You need BOTH — one on line 1 and one after `ChartExpert`. These mark the YAML settings block. |
| Indentation on agent names | Use exactly **2 spaces** before each dash, then a **space** after the dash, then the name. |

**Save the file** (`⌘+S` / `Ctrl+S`)

---

## Step 2: Create the Data Cleaner Agent

1. Press `Ctrl+Shift+P` → type **Chat: New Custom Agent** → select it
2. Choose **.github/agents**
3. Type **DataCleaner** and press Enter

**Alternatively:** Right-click the `.github/agents/` folder → New File → type `DataCleaner.agent.md`

### What to put in the file

```yaml
---
name: DataCleaner
description: I fix messy data by finding and correcting errors.
tools: ['read', 'edit']
user-invocable: false
---
```

```markdown
# Data Cleaner

You are a specialist in cleaning and fixing messy datasets.

When given data, look for:

1. Missing or blank values
2. Inconsistent date formats
3. NULL entries or placeholder text
4. Duplicate rows

Fix the issues and explain what you changed.
```

> ⚠️ **Important:** The YAML block and the system prompt go in the same file, one after the other.

### Key details

- **`user-invocable: false`** means only the AnalyticsLeader can call this agent. You cannot talk to it directly in chat. This is what makes it a *sub*-agent.
- This agent does NOT have the `agent` tool — it cannot delegate to other agents. It's a worker, not a manager.

**Save the file** (`⌘+S` / `Ctrl+S`)

---

## Step 3: Create the Chart Expert Agent

1. Press `Ctrl+Shift+P` → type **Chat: New Custom Agent** → select it
2. Choose **.github/agents**
3. Type **ChartExpert** and press Enter

**Alternatively:** Right-click the `.github/agents/` folder → New File → type `ChartExpert.agent.md`

### What to put in the file

```yaml
---
name: ChartExpert
description: I suggest and create data visualizations.
tools: ['read', 'execute']
user-invocable: false
---
```

```markdown
# Chart Expert

You are a specialist in data visualization.

When given clean data:

1. Suggest the best chart type for the data
2. Write the visualization code
3. Never modify the raw data — only read it

Always use clear labels, titles, and legends in your charts.
```

> ⚠️ **Important:** The YAML block and the system prompt go in the same file, one after the other.

**Save the file** (`⌘+S` / `Ctrl+S`)

---

## Step 4: Create the Skill File (Shared Handbook)

Skills are shared rules that ALL agents can access. Unlike agents, skills don't do anything on their own — they're reference documents.

1. In the Explorer sidebar, right-click the **.github** folder
2. Select **New Folder** → name it **skills**
3. Right-click the new **skills** folder → **New File** → name it `SKILL.md`

### What to put in the file

```yaml
---
name: skills
description: Standards and guidelines for the analytics team
---
```

```markdown
# Analytics Team Handbook

## Chart Standards
- Always use the company's official colors: #2E86AB (blue), #A23B72 (magenta), #F18F01 (orange)
- Every chart must have a clear title, axis labels, and a legend
- Use clean, minimal styling — no 3D effects

## Data Standards
- Dates should always be formatted as YYYY-MM-DD
- Currency values should include two decimal places
- Never delete raw data — always work on a copy

## General Rules
- Explain every change you make
- If something is unclear, ask before assuming
```

> ⚠️ **Important:** The YAML block and the handbook content go in the same file, one after the other. The skill file **MUST** have both a `name` and `description` field in the YAML block, or you will get an error.

**Save the file** (`⌘+S` / `Ctrl+S`)

---

## Step 5: Run the Team! 🚀

1. Open the **Chat panel** in VS Code (click the chat icon in the sidebar or press `⌘+Shift+I` / `Ctrl+Shift+I`)
2. Make sure you are in **Agent mode** — look for a mode dropdown at the top of the chat panel. If it says "Ask" or "Edit," switch it to "Agent."
3. Type your prompt directly into the chat:

```
Clean the sales data in sales_q1.csv and show me a trend of our revenue.
```

4. Press Enter

### What should happen

1. The **AnalyticsLeader** receives your request
2. It delegates to **DataCleaner** to fix the messy CSV (inconsistent dates, missing values, NULLs)
3. It then delegates to **ChartExpert** to create a revenue trend visualization
4. You see the final assembled result in chat

### If agents don't appear when typing @

1. Make sure all files are **saved**
2. Try reloading VS Code: `Ctrl+Shift+P` → **Developer: Reload Window**
3. Verify you're in **Agent mode** (not Ask or Edit mode)
4. Check that your GitHub Copilot extension is up to date (you may need the pre-release version)

---

## Your Final Folder Structure

```
sub-agents-demo/
├── .github/
│   ├── agents/
│   │   ├── AnalyticsLeader.agent.md
│   │   ├── DataCleaner.agent.md
│   │   └── ChartExpert.agent.md
│   └── skills/
│       └── SKILL.md
├── sales_q1.csv
├── CHEAT_SHEET.md          ← you are here!
└── README.md
```

---

## Quick Reference: Agent File Anatomy

Every `.agent.md` file has two parts:

**Part 1 — YAML Settings** (between `---` fences):

| Field | Purpose | Required? |
|-------|---------|-----------|
| `name` | The agent's identifier | Yes |
| `description` | What the agent does (one sentence) | Yes |
| `tools` | Capabilities granted to the agent | Yes |
| `agents` | List of sub-agents it can call | Leader only |
| `user-invocable` | Set to `false` for sub-agents | Sub-agents only |

**Part 2 — System Prompt** (below the closing `---`):

Plain English instructions telling the AI how to behave. Use Markdown formatting (headings, lists, bold) for clarity.

---

## Available Tools Reference

| Tool | What It Does |
|------|-------------|
| `agent` | Can delegate tasks to other agents |
| `read` | Can read files in your workspace |
| `edit` | Can modify files in your workspace |
| `search` | Can search your codebase |
| `execute` | Can run code (e.g., Python scripts) |
| `vscode` | Can interact with VS Code features |
| `web` | Can search the web |
| `todo` | Can manage task lists |

---

## Key Concepts

| Concept | What It Means |
|---------|--------------|
| **Agent** | A team member with a role and decision-making ability (`.agent.md` file) |
| **Skill** | A shared reference document any agent can access (`SKILL.md` file) |
| **YAML front matter** | The settings block between `---` fences at the top of each file |
| **System prompt** | The plain English instructions below the closing `---` fence |
| **Sub-agent** | A specialist agent with `user-invocable: false` that only the Leader can call |
| **Agent mode** | The chat mode in VS Code that enables custom agent interactions |

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Red squiggly lines on agent names in the Leader file | The sub-agent files haven't been created yet, or their names don't match exactly |
| "Skill must provide a name" error | Add a YAML block with both `name` and `description` fields at the top of your SKILL.md |
| Agents don't appear when typing `@` in chat | Reload VS Code (`Ctrl+Shift+P` → Developer: Reload Window), verify you're in Agent mode, check all files are saved |
| Copilot autocomplete suggests complex agent format | Dismiss it (press Escape) — use the simple list format with just agent names |
| YAML errors or red highlighting | Check indentation: use exactly 2 spaces before dashes in lists, ensure both `---` fences are present |
| "Configure Tools..." appears in your file | That's a VS Code UI overlay, not actual file content — ignore it |
