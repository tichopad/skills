---
name: create-plan
description: Create detailed implementation plans through interactive research and iteration
agent: plan
---

# Implementation Plan

Create detailed, phased implementation plans by researching the codebase and collaborating with the user.

## Process

### 1. Gather Context

- If a file path or goal description was provided as a parameter, read it fully. Otherwise, ask the user what we're building.
- Read all referenced files completely — never partially.
- Use Explore agents in parallel to find relevant source files, trace data flow, and understand current implementation patterns. Return file:line references.
- Read all files identified by research into the main context.

### 2. Present Understanding & Ask Questions

After research, present what you found:

```
Based on the goal and my research, I understand we need to [summary].

I found:
- [Current implementation detail with file:line reference]
- [Relevant pattern or constraint]

Questions my research couldn't answer:
- [Question requiring human judgment]
```

Only ask questions you genuinely cannot answer through code investigation.

### 3. Research & Design Options

After clarifications:

- If the user corrects a misunderstanding, verify with code before proceeding.
- Spawn parallel research agents for different aspects.
- Present findings with design options (pros/cons) and get alignment on approach.

### 4. Present Plan Summary

Before writing the full plan, present a concise summary:

- Key design decisions made
- Proposed phases with one-line descriptions
- If any decisions remain, provide numbered questions with short lettered options

Wait for user approval before writing.

### 5. Write the Plan

Ask the user to switch to the `build` agent. Once switched, write to `plan.md` using this structure:

```markdown
# [Feature] Implementation Plan

## Overview

[What and why, 2-3 sentences]

## Current State

[What exists, what's missing, key constraints with file:line references]

## Desired End State

[Specification of the outcome and how to verify it]

## Out of Scope

[Explicitly list what we're NOT doing]

## Phase 1: [Name] ⏳

### Overview

[What this phase accomplishes]

### Changes

- **`path/to/file.ext`**: [description of changes, with code snippets where helpful]

### Verification

- [ ] `pnpm check` passes
- [ ] [specific test or check]
- [ ] **Manual**: [UI/UX verification step]

> Pause after this phase for manual confirmation before proceeding.

---

## Phase 2: [Name] ⏳

[Same structure...]

## References

- [Links to related files, issues, research]
```

### 6. Iterate

Present the draft location and iterate based on feedback until the user is satisfied.

## Guidelines

- Be skeptical: Question vague requirements. Don't assume — verify with code.
- Be interactive: Don't write the full plan in one shot. Get buy-in at each step.
- Be thorough: Include file:line references. Write measurable success criteria.
- Be practical: Phases should be roughly equal in scope, each independently deliverable and testable.
- No open questions in final plan: Resolve all questions before finalizing. Stop and ask if uncertain.
