---
name: implement-phase
description: Implement a phase of technical plan with verification
agent: build
---

# Implement Phase

Implement a single phase of an approved implementation plan, verify the work, and produce a manual test checklist.

**Expected input**: `<path-to-plan>` (e.g., `plan.md`)

If the argument is missing, default to `plan.md`. If it's unavailable, ask for the plan path before proceeding.

## 1. Read & Understand

- Read the plan file completely — never partially.
- Determine the target phase:
  - If a phase heading ends with `🔄`, it is in-progress — resume that phase from the first unchecked item.
  - Otherwise, pick the first phase heading ending with `⏳` (waiting).
  - If no `⏳` phases remain (all are `✅`), tell the user all phases are complete and stop.
- Display which phase you selected and ask the user to confirm before proceeding.
- Check for `✅` on earlier phase headings — trust completed phases and start from the target.
- Read the original ticket/issue if referenced in the plan.
- Read **all files mentioned** in the target phase fully — no limit/offset.
- Think about how the changes fit into the broader codebase before writing any code.

## 2. Mark Phase In-Progress

- In the plan file, replace the `⏳` suffix on the target phase heading with `🔄` to indicate work is underway.
- If the phase is already marked `🔄`, it was partially implemented in a prior conversation — continue from the first unchecked item.

## 3. Implement

- Follow the plan's intent while adapting to what you actually find in the code.
- Implement changes methodically — complete one file or logical unit before moving to the next.
- Track progress with todos as you go.

### When Reality Doesn't Match the Plan

If something doesn't match what the plan describes, **stop and present the mismatch clearly**:

```
Issue in Phase [N]:
Expected: [what the plan says]
Found: [actual situation]
Why this matters: [impact on the implementation]

Suggested resolution: [your recommendation]
```

Wait for user input before proceeding past the mismatch.

## 4. Verify Automatically

After implementing, run all automated checks you can:

- `pnpm check` (covers typecheck + lint)
- Any phase-specific verification commands from the plan's Verification section
- Fix issues yourself — iterate until automated checks pass

## 5. Update the Plan

Reflect your progress in the implementation plan document:

- Check off (`- [x]`) completed items within the phase's Changes and automated Verification items.
- Do **not** mark the phase itself as complete — that's for the close-phase command after manual verification.
- Do **not** check off manual verification items.

## 6. Compose Manual Test Plan

Write a `test-plan.md` file with a concise checklist of what needs human verification.

Rules:

- **5–8 checkbox lines max**, one line per test — no headings, no descriptions.
- Only include tests that genuinely require a human (UI behavior, visual checks, flows that need a browser).
- Every check that can be done programmatically should already be done in step 3 — don't duplicate those here.

Example format:

```markdown
- [ ] Product page shows updated price format
- [ ] Cart updates correctly when quantity changes
- [ ] Mobile nav menu opens and closes smoothly
```

## 7. Report

Summarize what was done:

```
Phase [N] implemented — automated checks passing.

Changes made:
- [Brief list of what changed]

Please verify the manual test plan in test-plan.md.
Let me know when testing is complete so I can mark the phase as done.
```
