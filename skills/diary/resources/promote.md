# promote

Graduate ideas from daily notes into permanent vault notes.

## Core Function

Scan 14 days of daily notes to identify ideas worth developing into permanent notes, then facilitate graduation.

## Four-Step Workflow

### Step 1: Scan & Extract

Use Obsidian CLI to read recent daily notes and identify candidates via:

**Explicit markers:**
- `#idea`, `#expand` tags
- Phrases like "worth investigating"

**Implicit signals:**
- High-energy passages
- Original frameworks
- Recurring themes (3+ mentions)

**Exclude:**
- Tasks
- Meeting notes
- Complaints without insights
- Duplicates

### Step 2: Cross-Reference

Check vault for existing coverage, categorize:
- **New concept** (create note)
- **Underdeveloped** (enrich existing)
- **Already covered** (skip or flag additions)
- **Recurring unresolved** (high priority)

### Step 3: Present in Table

Display candidates ranked by priority, including:
- Source dates
- Status
- Quotes from daily notes (max 125 chars per excerpt)

### Step 4: Graduate Selected Ideas

**For new notes:**
- Write mini-essays (3-8 paragraphs)
- Preserve original voice
- Add backlinks
- Update source daily notes

**For existing notes:**
- Append dated content sections
- Add new links

**Update MOCs** if applicable

### Step 5: Report Results

Summarize:
- Graduated notes
- Remaining queue items
- Vault health metrics (ideas found vs. graduated, tagged vs. untagged gap, recurring themes pending)

## Key Principles

- Complete runs in 5-10 minutes
- Preserve original thinking voice
- Ask before modifying files
- Present borderline cases for user decision

Interact with the vault via the file system: walk `{diary-path}` to find recent entries, read files by path, and search file contents with grep/ripgrep.
