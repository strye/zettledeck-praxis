---
name: plan
description: Planning and priority management — reshape the week around active intellectual threads, review ruthless priorities, run quarterly RP reviews, and detect alignment gaps between stated priorities and actual behavior. Four modes covering weekly restructuring, weekly RP review, quarterly strategic review, and 30-60 day alignment analysis.
---

# Plan — Reshape, Review, Prioritize

Planning and priority management skill. Four modes covering weekly restructuring, RP progress review, quarterly strategic review, and alignment analysis.

## Invocation

```
/plan reshape-week    → Restructure the week around active intellectual threads
/plan review          → Weekly RP progress check and stall detection
/plan quarterly       → Quarterly RP review and refresh
/plan align           → Compare stated priorities against actual behavior (30-60 days)
```

## Mode Detection

| Mode | Trigger |
|------|---------|
| `reshape-week` | User says "reshape", "restructure", "plan the week", or `/plan reshape-week` |
| `review` | User says "review priorities", "RP check", "weekly review", or `/plan review` |
| `quarterly` | User says "quarterly review", "RP refresh", or `/plan quarterly` |
| `align` | User says "align", "check alignment", "priorities vs behavior", or `/plan align` |

If ambiguous, ask which mode.

## Requirements

**Tools needed:** Read, Write, Glob, `obsidian` CLI, `mcp__outlook`

**Reference files:**
- `references/rp-framework.md` — Ruthless Priorities framework (Azzarello RISE methodology)
- `references/task-operations.md` — Where tasks live, archival rules, RP alignment

---

## Mode: reshape-week

Restructure the week around active intellectual threads. Examines vault content before calendar changes through five phases.

### Phase 1: Vault-First Understanding

**Structural analysis:**
- Tags, orphaned notes
- Daily note review (past 14 days)
- Energy patterns, recurring investigations, stated intentions

**Context files:**
- Priorities and confidence markers

**Deep vault exploration:**
- Follow backlinks 2-3 hops
- Map active investigations, near-breakthrough ideas, stalled threads, convergence points

**Output:**
- 3-5 active threads with current state and advancement needs
- Near-breakthrough candidates
- Stalled threads worth reviving
- Meta-patterns emerging

### Phase 2: Calendar Reality

Pull:
- Calendar events (7 days)
- Tasks (overdue, due soon, floating)
- Recent email for time-sensitive threads

### Phase 3: Triage

Evaluate each event against intellectual map:

**Categories:**
- **KEEP** (serves active thread)
- **COMPRESS** (shorten)
- **MOVE** (reschedule)
- **DELEGATE**
- **ASYNC** (replace with document)
- **CANCEL**

Create delegation map (who handles which domains)

### Phase 4: 7-Day Plan

Structure each day with:
- Theme tied to active threads
- Protected 2-3hr deep work blocks
- Event table showing status and reasoning
- Delegation briefs
- Async artifacts

**Summary:**
- Meeting hours before/after
- Deep work blocks added
- Which threads receive dedicated time

### Phase 5: Execution

After explicit approval:
- Implement calendar changes
- Create deep work blocks
- Draft delegation emails
- Produce async replacements

### Key Principles

- Intellectual map from Phase 1 is foundational
- Calendar decisions must reference vault findings
- Deep work blocks name specific threads and intended outputs
- Delegation briefs are complete and standalone
- All output remains text-based with direct language

Use `obsidian` CLI for vault analysis and `mcp__outlook` for calendar.

---

## Mode: review

Weekly RP progress check. Run during or after the weekly diary review.

### Steps

1. **Read the RP registry** at `{rp-path}/ruthless-priorities.md` and each active RP's individual file.

2. **Check progress for each active RP:**
   - Were there action items, waiting-for items, or meeting notes related to this RP this week?
   - Did any tasks tagged `#rp/{rp-slug}` get completed?
   - Was there any diary mention of RP-related work?

3. **Surface stalls:**
   - If an RP had no progress, apply Azzarello's test: "No progress on {RP} this week. Is it still a Ruthless Priority, or was it just something important that you didn't get done?"
   - Do not guilt — name it factually and ask if the RP needs re-evaluation.

4. **Append progress log entry** to each active RP's individual file:
   ```markdown
   ### {YYYY}-W{WW} (weekly review)
   - {bullet summary of activity}
   ```
   Prepend new entries at the top of the Progress Log section (newest first).

5. **Present summary** showing each RP's status, this week's activity, and any flags.

---

## Mode: quarterly

Full quarterly RP review and refresh. Triggered when the `next-review` date in the registry's frontmatter arrives, or on user request.

### Steps

1. **Read full RP state** — registry file, all individual RP files, progress logs.

2. **For each active RP, assess:**
   - Overall progress since last quarterly review
   - Whether it's still the highest-impact use of strategic focus
   - Whether resources are adequate (Azzarello: "An RP without protected resources is a wish")

3. **Present review:**
   - Each RP with status, cumulative progress, and recommendation (continue, adjust, retire)
   - Parked/Rejected items worth reconsidering
   - Open slot analysis if fewer than 3 active RPs

4. **After user decisions:**
   - Update registry file with new status, adjusted descriptions, or retired RPs
   - Update `next-review` date in registry frontmatter
   - Append quarterly log entry to each RP file

### Boundaries

- Do not create or modify Ruthless Priorities without the user's explicit direction
- Do not demote an RP to Parked or Rejected without the user's confirmation
- Do not add more than 3 active RPs

---

## Mode: align

**Full instructions:** Load `resources/align.md`

Compare stated intentions against actual behavior to identify gaps and avoidance patterns. 30-60 day analysis. Compassionate honesty — the goal is awareness, not guilt.

Complement to `review` (weekly) and `quarterly` — where those modes assess RP progress, `align` surfaces the behavioral patterns underneath: what you said mattered vs. what actually received attention.
