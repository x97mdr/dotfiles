Run a Three Amigos refinement review for the Jira epic: $ARGUMENTS

Follow these steps in order:

## Step 1 — Fetch
Use @agent-jira-fetcher to retrieve the epic and all child stories from Jira.
Pass the epic key from the arguments (e.g. "PROJ-123").

## Step 2 — Review (run all three in parallel if possible)
Pass the full fetched content to all three reviewers:
- @agent-product-manager — business value and requirements quality
- @agent-developer — technical feasibility and clarity
- @agent-tester — testability and quality coverage

## Step 3 — Synthesize
Produce a consolidated summary showing:
- Per-story verdict table (PM | Dev | QA | Overall)
- Points of agreement across all three perspectives
- Conflicting assessments that warrant team discussion
- The most critical open questions to resolve
- Overall epic readiness: % of stories READY

## Step 4 — Confirm before writing
Present the synthesis to the user and ask: 
"Shall I write these results back to Jira? (yes/no)"

## Step 5 — Write back (only after confirmation)
Use @agent-jira-writer to post the review results as comments and labels on each story and the epic.

Report the Jira URLs of all updated issues when complete.
```

---

## How You'd Use It
```
/three-amigos PROJ-42