# R Suite Analysis Advice Mode Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add concise, data-specific analysis recommendations at basic, intermediate, and advanced levels without weakening r-suite's minimal R behavior.

**Architecture:** Add one silent routing mode and one compact advice workflow to `SKILL.md`. Extend regression prompts to verify that advice is specific to the available variables, states assumptions, and avoids recommending advanced methods without justification.

**Tech Stack:** Markdown skill instructions, JSON regression prompts, OpenAI skill metadata.

---

### Task 1: Define Analysis Advice Behavior

**Files:**
- Modify: `r-suite/SKILL.md`

- [x] Add `Analysis Advice` to silent routing.
- [x] Define when advice is proactive and when it stays silent.
- [x] Require suggestions to use observed data structure and be grouped into basic, intermediate, and advanced levels.
- [x] Limit each level to the few highest-value methods and explain question, reason, requirements, and next step.
- [x] Add failure modes for missing metadata, unsupported advanced methods, causal claims, and multiple testing.

### Task 2: Add Regression Prompts

**Files:**
- Modify: `r-suite/test-prompts.json`

- [x] Add a prompt with known ecological data fields that expects data-specific tiered suggestions.
- [x] Add a prompt with insufficient metadata that expects one concise clarification instead of a generic methods list.
- [x] Parse the JSON and confirm all prompt objects contain `id`, `prompt`, and `expected`.

### Task 3: Update User-Facing Metadata

**Files:**
- Modify: `r-suite/agents/openai.yaml`
- Modify: `README.md`

- [x] Mention analysis recommendations in the skill description and default prompt.
- [x] Add a short README example showing the basic/intermediate/advanced advice behavior.

### Task 4: Validate And Synchronize

**Files:**
- Verify: `r-suite/SKILL.md`
- Verify: `r-suite/test-prompts.json`
- Sync: `C:\Users\Administrator\.codex\skills\r-suite\`

- [x] Validate frontmatter, line count, description length, section presence, and JSON parsing.
- [x] Confirm repository and active skill copies match.
- [x] Update the GitHub repository and verify the remote files.
