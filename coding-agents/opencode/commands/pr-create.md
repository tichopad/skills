---
name: pr-create
description: Creates a DRAFT GitHub PR.
agent: build
---

# pr-create

Create DRAFT PR via `gh`. Title = `<JIRA> <conventional-commit>`. Body = `## Summary` + `## Details`.

## Invocation

Slash-command only. Do NOT auto-invoke. Skip unless user typed `/pr-create`.

## Args

Optional base branch override, e.g. `/pr-create develop`. Default base: `main`.

## Steps

### 1. Extract JIRA ticket if available

Get branch:

```bash
git rev-parse --abbrev-ref HEAD
```

Match `^[A-Z]+-\d+` at start of branch name (e.g. `XYZ-16177-fix-renovate` → `XYZ-16177`).

No match → ask user for ticket. Wait for answer. Do not proceed without ticket.

### 2. Pre-flight analysis

Run in parallel:

```bash
git status
git diff <base>...HEAD
git log <base>..HEAD --oneline
git rev-parse --abbrev-ref --symbolic-full-name @{u} 2>/dev/null || echo "no-upstream"
```

`<base>` = arg if provided, else `main`.

Read ALL commits in range, not just latest. Draft title + body from full diff.

### 3. Push branch

Upstream missing OR local ahead → push:

```bash
git push -u origin HEAD
```

### 4. Draft title

Format: `<JIRA> <type>: <subject>`

- `<type>`: conventional commit type (`feat`, `fix`, `chore`, `refactor`, `docs`, `test`, `perf`, `build`, `ci`, `style`). Pick from diff nature.
- `<subject>`: short, lowercase, imperative. No trailing period.
- Full title ≤ 70 chars.

Examples:

- `XYZ-16177 fix: handle Auth0 getSession failures in auth middleware`
- `XYZ-12345 feat: add zod validation to telemetry endpoints`

### 5. Draft body

Template:

```markdown
## Summary

- <high-level change 1>
- <high-level change 2>
- <...>

## Details

Linked to <JIRA>

<optional deeper explanation of one complex topic, only if warranted>
```

Rules:

- **Summary**: concise bullets. High-level changes. No file lists. No implementation trivia.
- **Details**: ALWAYS include `Linked to <JIRA>` line. Add deeper prose ONLY when one topic genuinely needs it (non-obvious tradeoff, unusual constraint, scope boundary). Otherwise leave just the link line.
- Keep short. Reviewer-facing overview, not exhaustive changelog. User adds line comments later.
- No "Test plan" section. No marketing fluff. No emojis.

### 6. Create PR

```bash
gh pr create --draft --base <base> --title "<title>" --body "$(cat <<'EOF'
## Summary

- ...

## Details

Linked to <JIRA>
EOF
)"
```

Return PR URL.

## Example

Branch: `XYZ-16177-auth-hardening`
Base: `main` (default)

Title: `XYZ-16177 fix: harden auth middleware and validate request bodies`

Body:

```markdown
## Summary

- Catch Auth0 `getSession` failures in auth middleware and respond with 503 instead of a 500 crash
- Validate request bodies in `post` and `/data.post` with zod via `readValidatedBody`

## Details

Linked to XYZ-16177

The endpoint is a generic GraphQL proxy, so the zod schema intentionally stays loose - it only checks structural shape (`query` is a non-empty string, optional `variables` object, optional `operationName`) rather than enumerating allowed operations. Goal is to reject obviously malformed payloads before they hit the upstream.
```

## Guardrails

- Draft PR only (`--draft`).
- Never force-push. Never `--no-verify`.
- Never commit unrelated staged changes. If `git status` shows dirty tree beyond this branch's work, stop and ask.
- If base branch = current branch, stop and ask.
- If no commits ahead of base, stop and ask.
