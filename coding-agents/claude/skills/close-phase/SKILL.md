---
name: close-phase
description: Close an implemented phase of technical plan
disable-model-invocation: true
---

# Close Phase

**Expected input**: `<path-to-plan>` (e.g., `plan.md`)

If the argument is missing, ask for it before proceeding.

## 1. Determine Current Phase

- Read the plan file completely.
- Find the phase whose heading ends with `🔄` (in-progress).
  - This is the phase being closed.
  - If no phase heading ends with `🔄`, tell the user there is no in-progress phase to close and stop.
  - If all phase headings end with `✅`, tell the user all phases are complete and stop.

## 2. Verify Test Plan

- Read `test-plan.md`.
- Check whether every item is marked done (`- [x]`).
- For each **unchecked** item, ask the user (using the available tool) whether it passed — phrase it as a yes/no question including the test description.
  - If the user confirms it passed, check it off in `test-plan.md`.
  - If the user says it did **not** pass, stop the close process and tell the user the phase cannot be closed until the failing item is resolved.

## 3. Archive Test Plan

- Rename `test-plan.md` to `test-plan-phase-<N>.md` where `<N>` is the phase number being closed.

## 4. Mark Phase Complete

- In the plan file, replace the `🔄` suffix on the phase heading with `✅`.

## 5. Commit

- Stage **all** changed and renamed files related to this close (plan file, archived test plan, any other files modified during the phase).
- Create a commit following repository commit conventions (see past commits). The short (title) line **must** include the phase number (e.g., `feat: complete phase 1 — CTA block config`).
- Do **not** push unless the user explicitly asks.
