# align

Compare stated intentions against actual behavior to identify gaps and avoidance patterns.

## Core Purpose

This mode performs a 30-60 day analysis comparing what you say matters to what actually received attention. It's a self-reflection tool that surfaces:
- Misalignment between priorities and action
- Active avoidance vs. passive neglect
- Unfulfilled commitments
- Unplanned energy leaks

**Key Principle:** Compassionate honesty. The goal is awareness, not guilt.

## Four-Step Process

### Step 1: Gather Stated Intentions

Extract priorities and commitments from context files and daily notes.

**Sources to Check:**

**1. Ruthless Priorities:**
- Read `{rp-path}/ruthless-priorities.md` directly
- What are the stated 1-3 ruthless priorities?
- What timeframe? (quarter, half-year)
- What's explicitly said to be important?

**2. Context Files:**
- Project MOCs
- Scope overviews
- Weekly/monthly plans
- OKRs or goal documents

**3. Daily Notes (past 30-60 days):**
Search for commitment language in diary files using grep/ripgrep:
- `"I need to"`, `"I should"`, `"priority"`, `"important"`

**4. Items Marked as Active/Current:**
- Projects tagged as "active"
- Tasks marked "high priority"
- Explicitly stated goals

**Output for Step 1:**
```
Stated Intentions (Past 30-60 Days):

Ruthless Priorities:
1. [RP 1] - [Description]
2. [RP 2] - [Description]
3. [RP 3] - [Description]

Project Commitments:
- [[Project A]] - marked "active" on [date]
- [[Project B]] - "need to finish" mentioned [X] times

Personal Commitments:
- [Commitment from daily notes with date]
- [More commitments...]

Total Intentions Identified: [count]
```

### Step 2: Gather Actual Behavior

Analyze what actually received attention through calendar, tasks, notes, and vault activity.

**Data Sources:**

**1. Calendar Time Allocation:**
```bash
# Use MCP outlook if available
mcp__outlook__list_events [date range]
```
- Where did time actually go?
- Meeting density by topic/project
- Time blocks by theme

**2. Task Completion Rates:**
- Review completed tasks from actions dashboard
- What got done vs. what was planned?
- Completion patterns by category

**3. Daily Note Patterns:**
Read past 30-60 days of diary:
- What topics appear repeatedly?
- What gets mentioned vs. what gets worked on?
- Energy and attention indicators

**4. Vault Activity:**
- Use the `vault` skill — `vault temporal-inventory {vault-path}` — to get time-ordered file activity (returns TSV: `date`, `file`, `doctype`, `status`, `word_count`)
- Walk vault directories and check file modification timestamps to identify recently created or edited notes
- Which scopes/projects have activity?
- Which are dormant?
- Note creation/edit patterns

**Output for Step 2:**
```
Actual Behavior (Past 30-60 Days):

Calendar Time Allocation:
- [Topic/Project A]: [X] hours ([Y]%)
- [Topic/Project B]: [Z] hours ([W]%)
- [Continue...]

Task Completion:
- Total completed: [count]
- By category: [breakdown]
- Completion rate: [percentage]

Daily Note Topics (frequency):
1. [Topic] - mentioned [X] times, worked on [Y] times
2. [Continue for top 10...]

Vault Activity:
- Notes created: [count] ([breakdown by scope])
- Notes edited: [count] ([breakdown by scope])
- Most active scope: [name]
- Dormant scopes: [list]
```

### Step 3: Message Review

Scan recent communications for commitment language to surface unfulfilled promises.

**Search Patterns:**

**Commitment Language in Email/Messages:**
- "I'll..."
- "I promised..."
- "I will..."
- "Following up on..."
- "I should have..."

**Method:**
- Review recent sent emails if MCP available
- Check diary entries for commitment notes
- Look for follow-up promises in meeting notes

**Categories:**
- Promises to others (external commitments)
- Promises to self (internal commitments)
- Follow-ups pending
- Unfinished conversations

**Output for Step 3:**
```
Commitment Scan:

External Commitments (to others):
- Promised [X] to [Person] on [date] - Status: [unfulfilled/partial/complete]
- [Continue...]

Internal Commitments (to self):
- Committed to [X] on [date] - Status: [unfulfilled/partial/complete]
- [Continue...]

Pending Follow-Ups:
- [Topic] with [Person] - last contact [date], no follow-through
- [Continue...]

Total Unfulfilled Commitments: [count]
```

### Step 4: Generate Drift Report

Create alignment scores and classify avoidance patterns.

**Alignment Scoring (1-10 scale):**

For each stated intention:
- **10:** Perfect alignment - stated + acted upon consistently
- **7-9:** Strong alignment - mostly acted upon
- **4-6:** Partial alignment - some action, inconsistent
- **1-3:** Weak alignment - little to no action
- **0:** Complete drift - stated but never acted upon

**Avoidance Classification Decision Tree:**

```
Is it mentioned recently (past 2 weeks)?
+-- NO -> Passive Neglect
+-- YES -> Are related items getting done?
    +-- YES -> Not avoided, just this specific item
    +-- NO -> Does it have clear next steps?
        +-- YES -> Active Avoidance
        +-- NO -> Stuck/Blocked
    Does it require others?
        +-- YES -> Dependency Block
        +-- NO -> Active Avoidance
```

**Classification Types:**

**Passive Neglect:**
- Not mentioned, not worked on
- Fallen off radar entirely
- No recent resistance or engagement

**Active Avoidance:**
- Mentioned frequently but no action
- Recurring "need to do this" without follow-through
- Visible resistance or procrastination

**Stuck/Blocked:**
- Mentioned but lacks clear next steps
- Uncertainty about how to proceed
- Waiting for clarity

**Dependency Block:**
- Requires others' input/action
- Waiting on external factors
- Can't proceed independently

**Output for Step 4:**
```
Drift Report

Alignment Scores:

Intention | Score | Status | Classification
----------|-------|--------|---------------
[RP 1]    | 9/10  | Strong | On track
[RP 2]    | 4/10  | Partial | Stuck/Blocked
[RP 3]    | 1/10  | Weak   | Active Avoidance
[Project A]| 7/10 | Strong | On track
[Project B]| 0/10 | None   | Passive Neglect
[Continue for all intentions...]

Average Alignment Score: [X/10]
```

## Extended Analysis

### Unplanned Energy Leaks

Where is time/energy going that wasn't explicitly prioritized?

**Method:**
- Compare calendar topics to stated priorities
- Identify activities not in any intention list
- Calculate percentage of time on unplanned items

**Output:**
```
Unplanned Energy Leaks:

Time spent on non-prioritized activities: [X]%

Top Leaks:
1. [Activity] - [X] hours ([Y]% of time) - Not in stated priorities
2. [Continue...]

Assessment:
- Are these valuable? (emergent priorities worth acknowledging)
- Are these drains? (should be reduced/eliminated)
- Are these necessary? (operational overhead)
```

### Active Avoidance Deep Dive

For items classified as "Active Avoidance":

**Questions to explore:**
1. What's the pattern? (how long has this been avoided?)
2. What's the resistance? (why is this being avoided?)
3. Is this worth keeping? (or should it be dropped?)

**Method:**
- Read diary entries mentioning the item
- Look for emotional language
- Identify recurring excuses or reasons

**Output:**
```
Active Avoidance Analysis:

[Item Name] - Alignment Score: [X/10]

Pattern:
- First mentioned: [date]
- Mentions: [count] over [timespan]
- Action taken: [minimal/none]
- Push-back cycle: [weekly/monthly]

Resistance Indicators:
- [Quote from diary showing resistance]
- [Pattern of excuses]
- [Emotional language used]

Assessment:
- Keep or Drop? [Recommendation]
- If Keep: [What needs to change?]
- If Drop: [How to gracefully abandon?]
```

### Unfulfilled Commitments to Others

External commitments requiring attention.

**Prioritize by:**
1. Importance of relationship
2. Time since commitment
3. Impact of non-delivery

**Output:**
```
Unfulfilled External Commitments (Priority Order):

1. [Commitment] to [Person]
   - Promised: [date]
   - Days overdue: [X]
   - Impact: [High/Medium/Low]
   - Recommended action: [Deliver/Renegotiate/Cancel]

2. [Continue...]
```

### Recurring Push-Back Cycles

Items that repeatedly appear then disappear.

**Pattern:**
- Mentioned -> planned -> not done -> mentioned again -> repeat

**Method:**
- Search diary for recurring phrases
- Identify weekly/monthly cycles
- Note what triggers mentions vs. what prevents action

**Output:**
```
Recurring Push-Back Cycles:

[Item Name]
- Cycle frequency: [weekly/bi-weekly/monthly]
- Mentions: [dates]
- Actions: [dates - if any]
- Pattern: [Description of cycle]
- Root cause: [Why does this keep happening?]
```

## Synthesis & Recommendations

### Honest Assessment

**What's working:**
- [High-scoring intentions]
- [Patterns of success]

**What's not working:**
- [Low-scoring intentions]
- [Patterns of avoidance]

**What's surprising:**
- [Unexpected findings]
- [Discrepancies between belief and reality]

### Specific Corrections

For each low-alignment item, recommend one of four actions:

**DROP:**
- Stop pretending this is a priority
- Remove from lists
- Free up mental space

**DO NOW:**
- Take immediate action
- Schedule within next 48 hours
- Break into smaller first step

**DELEGATE:**
- Identify who else could handle this
- Transfer ownership clearly
- Remove from your plate

**REFRAME:**
- Redefine the priority
- Adjust expectations
- Change approach or scope

**Output:**
```
Recommended Corrections:

DROP (3 items):
1. [Item] - Reason: [Why drop] - Action: [How to gracefully abandon]

DO NOW (2 items):
1. [Item] - Reason: [Why urgent] - Next step: [Specific action within 48h]

DELEGATE (1 item):
1. [Item] - Reason: [Why delegate] - To whom: [Person] - Handoff: [How]

REFRAME (2 items):
1. [Item] - Current frame: [X] - New frame: [Y] - Adjustment: [How]
```

## Complete Output Format

### Summary

**Analysis Period:** [Date range]
**Stated Intentions:** [count]
**Average Alignment Score:** [X/10]
**Overall Assessment:** [Strong/Moderate/Weak] alignment

---

### Alignment Scores Table

[From Step 4]

---

### Key Findings

**Unplanned Energy Leaks:** [percentage]%
[Top 3 leaks]

**Active Avoidance:** [count] items
[List items]

**Unfulfilled Commitments:** [count] to others, [count] to self
[Priority list]

**Recurring Cycles:** [count] patterns identified
[List patterns]

---

### Detailed Analysis

[Active Avoidance Deep Dive for each item]

[Unfulfilled External Commitments]

[Recurring Push-Back Cycles]

---

### Honest Assessment

[What's working, what's not, what's surprising]

---

### Recommended Corrections

[DROP, DO NOW, DELEGATE, REFRAME for each low-alignment item]

## Constraints

- Analysis period: 30-60 days (adjustable based on diary availability)
- Cite specific dates and data for all claims
- Compassionate tone - awareness not judgment
- Every low-alignment item must get a correction recommendation
- Distinguish active avoidance from passive neglect
- Include unfulfilled commitments to others separately

## Vault Structure Context

Our vault uses this structure:
- **Scopes:** `S{ID}_{Title}/` - Major life/work domains
- **Projects:** `P{ID}_{Title}/` - Multi-step outcomes
- **Daily Diary:** `{diary-path}/YYYY/MMM/YYYY-MM-DD_{Day}.md`
- **Ruthless Priorities:** `{rp-path}/ruthless-priorities.md`
- **Actions Dashboard:** `{actions-path}/`

Interact with the vault via the `vault` skill — `vault temporal-inventory {vault-path}` — for time-ordered file activity (returns TSV: `date`, `file`, `doctype`, `status`, `word_count`). Supplement with file system operations: read files by path, walk directory trees, and search file contents with grep/ripgrep. Use `mcp__outlook__list_events` for calendar analysis when available.

## Example Usage

**User invokes:** `/insight align`

**You should:**
1. Gather stated intentions from ruthless priorities, context files, daily notes (30-60 days)
2. Gather actual behavior from calendar, tasks, daily notes, vault activity
3. Scan communications for unfulfilled commitments
4. Generate alignment scores and classify avoidance patterns
5. Provide detailed analysis of active avoidance, unfulfilled commitments, recurring cycles
6. Offer honest assessment and specific corrections (DROP/DO NOW/DELEGATE/REFRAME)

**Remember:** Be compassionate. The goal is clarity, not criticism. Drift is normal; awareness enables choice.
