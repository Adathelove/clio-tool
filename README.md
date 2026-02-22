# Clio - Chronological Log Inspector and Observer

A comprehensive toolkit for managing, browsing, and aggregating `.agent.dir` logs across your filesystem and cloud storage.

## Overview

Clio provides tools to:
- **Find** all agent logs across local and cloud directories
- **Browse** logs chronologically with interactive navigation
- **Search** for keywords within logs and OneDrive files
- **Aggregate** daily insights into holistic delta logs
- **Maintain** the macOS locate database for fast searches

## Installation

1. Clone this repository:
   ```bash
   git clone https://github.com/adathelove/clio-tool.git ~/repos/git/adathelove/clio-tool
   ```

2. Add bin directory to your PATH in `~/.zshrc` or `~/.bashrc`:
   ```bash
   export PATH="$HOME/repos/git/adathelove/clio-tool/bin:$PATH"
   ```

3. Reload your shell:
   ```bash
   source ~/.zshrc  # or source ~/.bashrc
   ```

4. (Optional) Set up the locate database:
   ```bash
   update-locatedb --install
   ```

## Tools

### 1. `update-locatedb` - Manage macOS Locate Database

Maintains the macOS locate database for fast file searching.

```bash
# Update database now (requires sudo)
update-locatedb

# Install weekly automatic updates
update-locatedb --install

# Check database status
update-locatedb --status
```

**Features:**
- Weekly automatic updates via launchd (Sundays at 3 AM)
- Status checking for database freshness
- Integrated with macOS locate system

---

### 2. `find-agent-logs` - Find Agent Logs

Searches for all `.agent.dir/Logs/*.md` files across the filesystem.

```bash
# Find logs using find command (slower, current)
find-agent-logs --find

# Find logs using locate database (faster)
find-agent-logs --locate

# Include GDrive in search
find-agent-logs --find --gdrive

# Include all cloud directories
find-agent-logs --all

# Search specific directory
find-agent-logs --find ~/repos
```

**Features:**
- JSON output with metadata (date, project, path, modification time)
- Cloud directory support (GDrive, OneDrive)
- Fast locate-based search or comprehensive find-based search
- Extracts dates from log filenames

---

### 3. `clio` - Full-Featured Log Tool

Command-line interface for log operations.

```bash
# List all logs
clio list

# List logs from specific path
clio list ~/repos

# List logs with date filtering
clio list --from 2026-01-01 --to 2026-01-31

# Show specific log
clio show ~/.agent.dir/Logs/2026-01-23.md

# Search for keyword
clio search SUCCESS

# View timeline
clio timeline

# Find referenced logs
clio refs ~/.agent.dir/Logs/2026-01-23.md

# Interactive mode
clio interactive
```

**Features:**
- Multiple output modes (list, show, timeline)
- Keyword search across logs
- Reference detection between logs
- Date filtering
- Cloud directory support with `--gdrive` and `--all` flags

---

### 4. `clio-browse` - Interactive Log Browser

Interactive TUI for browsing logs chronologically with advanced search capabilities.

```bash
# Start browser (default: ~/repos)
clio-browse

# Include GDrive logs
clio-browse --gdrive

# Include all cloud sources
clio-browse --all

# Start from specific path
clio-browse ~/repos
```

**Interactive Commands:**

**Navigation:**
- `next`, `n` - Show next log
- `prev`, `p` - Show previous log
- `jump NUMBER` - Jump to specific log
- `list`, `ls` - Show all logs

**Content:**
- `pull` - Load current log content into context
- `show` - Refresh current log display

**Search in Logs:**
- `inner-find KEYWORD` - Search all logs (shows BEFORE/CURRENT/AFTER)
- `inner-forward KEYWORD` - Search forward from current log
- `inner-backward KEYWORD` - Search backward from current log
- `if`, `ifw`, `ibk` - Short aliases for above

**Search in OneDrive:**
- `onedrive-search KEYWORD` - Search ALL OneDrive files (not just logs)
- `od KEYWORD` - Short alias

**Other:**
- `help`, `h`, `?` - Show help
- `quit`, `q` - Exit

**Features:**
- Chronological navigation through all logs
- Context-aware keyword searching (shows position relative to current log)
- OneDrive full-text search for related work
- Preview mode shows first 15 lines of each log
- Pull mode loads complete log content

---

### 5. `search-onedrive` - OneDrive File Search

Searches ALL files in OneDrive for keywords (not limited to `.agent.dir/Logs`).

```bash
# Search for keyword in all OneDrive files
search-onedrive API

# Search markdown files only
search-onedrive deployment --type md

# Search documents
search-onedrive budget --type "doc*"
```

**Features:**
- Full-text search across all OneDrive files
- File type filtering
- Context display with line numbers
- Skips binary and very large files (>10MB)

---

### 6. `clio-delta` - Daily Aggregation Tool

Generates holistic daily views of all agent activity.

```bash
# Generate delta for today (end of day)
clio-delta --gdrive

# Generate for specific date
clio-delta --date 2026-01-23 --gdrive

# Include all sources
clio-delta --all
```

**Output File Format:** `deltas/YYYY-MM-DD.md` (ISO 8601 date, one per day)

**Delta Structure:**
1. Header with generation timestamp and log count
2. Executive Summary (projects, activity span)
3. Chronological Analysis (each log with context, ordered by timestamp)
4. Comprehensive Analysis (patterns, insights, continuity map)
5. **The Pick up Point** (end-of-day state for next-day resumption)
6. Summary Statistics

**Features:**
- Automatic extraction of SUCCESS/FAILURE/OPEN QUESTION sections
- Project-labeled insights with continuity paths
- Dependency chain discovery
- Holistic daily view across all work
- Supports cloud directories
- Chronological ordering by file modification time

**The Pick up Point:**
End-of-day section you fill in manually for tomorrow's resumption:
- State checks required (MRs merged? Branches pushed?)
- Open threads waiting on responses
- Questions to resolve
- Context for next session

See [DELTA-FORMAT.md](DELTA-FORMAT.md) for complete specification.

---

## Directory Structure

```
clio-tool/
├── bin/
│   ├── update-locatedb       # Locate database manager
│   ├── find-agent-logs        # Log finder (JSON output)
│   ├── clio                   # Full-featured CLI
│   ├── clio-browse            # Interactive TUI browser
│   ├── search-onedrive        # OneDrive file search
│   └── clio-delta             # Daily aggregation tool
├── deltas/                    # Daily aggregated logs
│   └── YYYY-MM-DD.md          # Delta logs (ISO 8601 format)
├── .agent.dir/                # Agent configuration
│   ├── ident.*.json           # Identity files
│   ├── Logs/                  # Clio's own logs
│   └── handoffs/              # Task coordination
├── CLAUDE.md                  # Bootstrap instructions
├── AGENTS.md                  # Agent behavior guidelines
├── DELTA-FORMAT.md            # Delta file format specification
└── README.md                  # This file
```

## Cloud Directory Support

Clio integrates with cloud storage:

### GDrive Integration
- Path: `~/Cloud/GDrive` → `~/Library/CloudStorage/GoogleDrive-...`
- Searches for `.agent.dir/Logs` just like local directories
- Use `--gdrive` flag to include in searches

### OneDrive Integration
- Path: `~/Cloud/OneDrive-Sturdy` → `~/Library/CloudStorage/OneDrive-...`
- **Different behavior:** Searches ALL files for keywords, not just logs
- Use `onedrive-search` command for full-text search
- Useful for finding related work outside of agent logs

### All Sources
- Use `--all` flag to include all cloud directories
- Automatically enabled with clio-delta

## Workflows

### Daily Review Workflow

1. Generate delta log for today:
   ```bash
   clio-delta --all
   ```

2. Review the delta:
   ```bash
   cat deltas/2026-01-23.md
   ```

3. Browse logs interactively if needed:
   ```bash
   clio-browse --all
   ```

### Finding Related Work

1. Start interactive browser:
   ```bash
   clio-browse --all
   ```

2. Navigate to relevant date:
   ```bash
   clio-browse> jump 3
   ```

3. Pull log content:
   ```bash
   clio-browse> pull
   ```

4. Search for keywords:
   ```bash
   clio-browse> if "authentication"
   ```

5. Search OneDrive for related docs:
   ```bash
   clio-browse> od "authentication"
   ```

### Historical Investigation

1. List logs in date range:
   ```bash
   clio list --from 2026-01-01 --to 2026-01-15 --all
   ```

2. Search across that range:
   ```bash
   clio search "bug fix" --from 2026-01-01 --to 2026-01-15
   ```

3. Generate deltas for review:
   ```bash
   for date in 2026-01-{10..15}; do
       clio-delta --date $date --all
   done
   ```

## Log Format

Agent logs follow this structure:

```markdown
# Daily Log - YYYY-MM-DD

**Chat**: 📁 Project Name
**Session**: Description

---

## SUCCESS: What Worked

Detailed description of success

---

## FAILURE: What Didn't Work

What went wrong and why

---

## OPEN QUESTION: Unresolved

What needs future resolution

---

## Context for Next Session

Key info for continuation
```

Clio tools parse these sections for aggregation and search.

## Performance Tips

### Use Locate for Speed
1. Run `update-locatedb --install` for automatic weekly updates
2. Use `--locate` flag with find-agent-logs and clio commands
3. Database updates happen Sundays at 3 AM automatically

### Scope Your Searches
1. Search specific directories instead of entire home: `clio list ~/repos`
2. Use date filtering to reduce result sets: `--from 2026-01-23`
3. Use `--find` for most current results, `--locate` for speed

### Cloud Integration
1. Only use `--gdrive` when you need GDrive logs
2. Use `onedrive-search` explicitly (not included in `--all` by default)
3. OneDrive searches can be slow - use file type filters

## Requirements

- macOS (tested on macOS 14+)
- Bash 3.2+ (default macOS bash)
- jq (install with `brew install jq`)
- Optional: locate database (managed by update-locatedb)

## Future Enhancements

Potential additions:
- Timeline visualization
- Export to various formats (JSON, CSV, HTML)
- Integration with other tools (git, jira, slack)
- AI-powered summarization of deltas
- Web interface for browsing
- Conflict detection across logs
- Task extraction and tracking

## Contributing

This is a personal tool but improvements are welcome! The codebase follows these principles:
- Bash scripts for portability
- JSON output for interoperability
- Modular design for composability
- Color-coded terminal output for readability

## License

BSD-3-Clause (to be added)

## Author

Ada Love - [ada.love@sturdy.ai](mailto:ada.love@sturdy.ai)

## Acknowledgments

Built using the AgentDir system for AI-assisted development. This tool itself generates logs that it can then inspect - a self-referential delight!
