Perform a tech-lead code review of the most recently committed story work in this repository.

The goal is to catch issues before they reach production — not to nitpick style. The
`tech-lead` agent owns the standards and the report format; this command just feeds it
the right diff and acts on what it finds. Do **not** restate CLAUDE.md here — it is the
single source of truth and the agent reads it.

---

## Step 1 — Establish the diff

- If $ARGUMENTS is a commit SHA or range (e.g. `HEAD~3..HEAD`), use that range.
- Otherwise, review the most recent commit: `git show HEAD --stat`, then `git diff HEAD~1..HEAD`.
- Note the story key(s) in the commit message(s) for context.

---

## Step 2 — Spawn the tech-lead agent

Spawn `@agent-tech-lead`, passing:

- The diff (or range/files) to review.
- The story key(s) and any acceptance criteria available for context.
- This instruction: "Read CLAUDE.md (repo root) and web/CLAUDE.md. Scan the codebase for
  established patterns in files similar to those changed. Then audit the diff against
  every standard in CLAUDE.md and produce your Blockers / Warnings / Suggestions report.
  Treat the Security rules as non-negotiable blockers."

The agent reads the standards, scans for patterns, and returns a structured report.

---

## Step 3 — Present the report

Show the agent's full report to the user verbatim.

---

## Step 4 — Act on blockers

For each Blocker:
- Make the fix immediately.
- State what was wrong and what changed.

Do not mark the story Done while any Blocker is open.

---

## Step 5 — Offer a re-run

After fixing blockers, offer to re-run the review to confirm it comes back clean.
