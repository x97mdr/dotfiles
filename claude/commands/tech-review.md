Perform a tech-lead code review of the most recently committed story work in this repository.

## What this review covers

Audit the diff against the project's non-negotiable standards from CLAUDE.md. The goal is to catch issues before they reach production — not to nitpick style.

---

## Step 1 — Establish the diff

Identify what to review:
- If $ARGUMENTS is a commit SHA or range (e.g. `HEAD~3..HEAD`), diff that range.
- Otherwise, diff the most recent commit: `git show HEAD --stat` then `git diff HEAD~1..HEAD`.
- Note the story key(s) mentioned in the commit message(s) for context.

---

## Step 2 — Read the standards

Read `/Users/jeffrey/Documents/GitHub/braggart/CLAUDE.md` in full before auditing. Every finding must cite a specific rule from that file.

---

## Step 3 — Audit the diff

Work through each changed file. Check every item in the lists below. Skip checks that are not applicable to the file type.

### Security (non-negotiable)
- [ ] `user_id` is always extracted from the verified Clerk JWT — never from request body, query params, or client-set headers
- [ ] Every protected endpoint calls `Depends(get_current_user)` before any other operation
- [ ] Every data mutation checks `resource.user_id == authenticated_user_id` server-side
- [ ] User-supplied URLs validated for `http`/`https` scheme only
- [ ] File MIME type and size validated server-side before any presigned S3 URL
- [ ] No secrets or API keys in source code

### Code philosophy
- [ ] No speculative abstractions — only what the current story requires
- [ ] No commented-out code
- [ ] No `TODO` without a linked Jira issue key (e.g. `# TODO BRAG-42`)
- [ ] No `print()` in production code (Python) — use `logging`
- [ ] No `console.log` in production code (TypeScript) — use `console.error` for real errors
- [ ] Error handling only at system boundaries (user input, external APIs, DB) — not wrapping internal calls defensively

### FastAPI
- [ ] Pydantic v2 models for all request/response bodies — no raw `dict` returns
- [ ] `Depends(get_current_user)` on every protected route
- [ ] Correct HTTP semantics: `201` for POST (creates), `200` for GET/PUT, `204` for DELETE/no-body, `400` for validation, `403` for ownership, `404` for not found
- [ ] One router file per domain — no cross-domain logic leaking
- [ ] DB access only through injected `Session` — no sessions instantiated inside endpoints
- [ ] `async def` for all I/O-bound endpoints

### SQLAlchemy / Alembic
- [ ] Explicit column types (`String(255)` not `String`, `DateTime(timezone=True)` not `DateTime`)
- [ ] FK constraints defined at ORM level
- [ ] `index=True` on every FK column
- [ ] `server_default=func.now()` for timestamp columns
- [ ] Every schema change has an Alembic migration — no manual `ALTER TABLE`
- [ ] Migrations are backward-compatible (nullable columns preferred over drops/renames when safe)

### Next.js
- [ ] Server Components by default — `"use client"` only where state/effects/browser APIs are required
- [ ] All FastAPI calls go through route handlers in `src/app/api/` — browser never calls FastAPI directly
- [ ] Route handlers extract Clerk token and forward as `Authorization: Bearer <token>`
- [ ] No inline styles — Tailwind utility classes only
- [ ] shadcn/ui for UI primitives — no reimplementing what shadcn provides
- [ ] `process.env.API_URL` only used server-side (route handlers, Server Components) — never in client components

### Testing
- [ ] Every new FastAPI endpoint has: at minimum one happy-path test + one auth/ownership test
- [ ] Every new `"use client"` component has: at minimum one render test + one interaction test
- [ ] Tests use real DB (not mocks) for API integration tests
- [ ] Mocks limited to: auth (`get_current_user`), S3, external HTTP calls (Resend, Clerk API, EventBridge)

---

## Step 4 — Report findings

Produce a report with three severity levels:

**🚨 Blocker** — Must be fixed before this work is considered Done. Violates a security rule or a non-negotiable standard.

**⚠️ Warning** — Should be fixed soon. Degrades quality or creates technical debt but isn't an immediate risk.

**💡 Suggestion** — Optional improvement. Low priority.

Format:

```
## Tech Review — [story key(s)] — [date]

### Summary
[1–3 sentences on what the diff does and overall quality]

### Findings

#### 🚨 Blockers
- **[File:line]** [Finding] — [Rule from CLAUDE.md]

#### ⚠️ Warnings
- **[File:line]** [Finding] — [Rule from CLAUDE.md]

#### 💡 Suggestions
- **[File:line]** [Finding]

### Checklist pass rate
X / Y checks passed (list any that were N/A)

### Verdict
PASS — no blockers found, safe to ship  
  or  
FAIL — [N] blocker(s) must be resolved before marking Done
```

If there are no findings in a severity level, omit that section.

---

## Step 5 — If blockers are found

List the exact changes needed to resolve each blocker. Offer to fix them now.
