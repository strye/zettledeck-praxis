# diary.close-day

End-of-day ritual for processing and filing daily notes.

## Overview

Structured ritual (~5 minutes) for extracting actionable items and maintaining vault connections. Focuses on filing and surfacing, not summarizing.

## Core Workflow Steps

### Step 1: Read Daily Note

Execute `obsidian daily:read` or read today's diary file to capture:
- Free-form writing
- Meeting notes
- Ideas
- Commitments
- Tasks

### Step 1.5: Vault Discovery

**Required queries:**
- Tag counts
- Theme-based searches
- Backlinks
- Orphan note detection

**Flag:**
- Confidence level shifts (compare today's language with context files)
- Contradictions between today's claims and previous entries

### Step 2: Extract & Categorize

**Categories:**
- **Action items** (promised tasks, follow-ups, deadlines)
- **Ideas & insights** (work patterns, project concepts)
- **People & commitments** (follow-ups, meetings needed)
- **Open questions** (investigations, pending decisions)

### Step 3: File Locations

Recommend destinations for each extracted item:
- Context files
- Google Tasks
- Related notes

Present in table format.

### Step 4: Backlinks Discovery

Query existing connections and identify terms requiring links to people, projects, or concepts.

### Step 5: Context Updates

Flag belief shifts, new constraints, resolved questions, or confidence marker changes needing context file updates.

### Step 6: Carry Forward

Identify:
- Unfinished priorities
- Momentum to maintain
- Draft answers to quick-wrap reflection questions if incomplete

## Output Structure

**Today's Extraction** -> **Vault Connections** -> **Filing Suggestions** -> **Backlinks** -> **Context Updates** -> **Carry Forward** -> **Quick Wrap responses**

Use `obsidian` CLI for daily note reading and vault queries.
