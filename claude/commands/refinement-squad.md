Perform a Three Amigos analysis on the feature described by `$ARGUMENTS`.

## Step 1 — Determine input mode

Inspect `$ARGUMENTS`:

- If it matches a Jira issue key pattern (e.g. `KAN-4`, `PROJ-123` — one or more letters, a hyphen, one or more digits), treat it as **Jira mode**.
- Otherwise treat the entire argument as a **plain-text description**.

## Step 2 — Gather the feature description

**Jira mode:**
Use the Atlassian MCP to fetch the epic. Extract the summary and full description. If the issue is not an Epic, note that and continue anyway using its summary and description.

**Plain-text mode:**
Use `$ARGUMENTS` directly as the feature description. There is no existing epic yet.

## Step 3 — Three Amigos analysis

Simulate a structured Three Amigos session with the following three perspectives. Be thorough — surface ambiguities, edge cases, and risks that a real session would catch.

### 🧑‍💼 Business Analyst / Product Owner
- What is the business goal and measurable value?
- Who are the users and what are their needs?
- What are the happy-path and unhappy-path scenarios?
- What are the boundaries — what is explicitly OUT of scope?
- What open questions need answers before development starts?

### 🧑‍💻 Developer
- What is the proposed technical approach?
- What are the dependencies and integration points?
- What are the implementation risks or unknowns?
- How should the work be broken into deliverable stories?
- What technical acceptance criteria are needed?

### 🧪 QA / Tester
- What test scenarios cover the happy path?
- What edge cases and boundary conditions need tests?
- What are the quality risks?
- What is the definition of done for each story?

## Step 4 — Produce stories

Based on the analysis, produce a prioritised list of user stories. For each story:

```
Title: <concise imperative title>
As a <role>, I want <goal> so that <benefit>.

Acceptance Criteria:
- [ ] ...

Technical Notes:
- ...

Test Scenarios:
- ...

Dependencies: <story titles this depends on, or "none">
```

End with a recommended **implementation order** with a one-line rationale for each position.

## Step 5 — Write to Jira (with permission)

**Jira mode — updating an existing epic:**
Present a summary of proposed changes (new stories to create, any suggested edits to the epic description). Ask:
> "Shall I create these stories in Jira under [EPIC-KEY]? (yes / no / edit first)"
Only proceed if the user confirms.

**Plain-text mode — creating from scratch:**
Present the proposed epic summary and all stories. Ask:
> "Shall I create the epic and these stories in Jira? If so, which project key should I use? (or type 'no' to skip)"
Only proceed if the user provides a project key or explicit confirmation.

When creating in Jira:
- Create the epic first, then each story as a child issue linked to the epic
- Use the story title as the Jira summary
- Put the full user story, AC, technical notes, and test scenarios in the description
- Report each created issue key as you go