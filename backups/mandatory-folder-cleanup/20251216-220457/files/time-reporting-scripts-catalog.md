# EVA Suite - Time Reporting Scripts Catalog

**Last Updated:** 2025-11-24  
**Location:** `eva-orchestrator/` root directory  
**Data File:** `work-sessions.json`

## Overview

The EVA Suite time reporting system tracks work sessions with start/end times, projects, tasks, and notes. All scripts operate on a single JSON file (`work-sessions.json`) that stores session data.

---

## ✅ Existing Scripts

### 1. `login.ps1` - Start Work Session
**Purpose:** Start a new work session with time tracking and system health checks

**Usage:**
```powershell
.\login.ps1
.\login.ps1 -Project "EVA Suite" -Task "Feature implementation"
```

**Features:**
- System health checks (git status, master plan, GitHub Pages, documentation)
- Prompts for project and task if not provided
- Detects and handles unclosed sessions
- Creates session entry in `work-sessions.json`
- Exports `$env:EVA_SESSION_ID` for reference

**Parameters:**
- `-Project` (string): Project/Sprint name
- `-Task` (string): Task description
- `-LogFile` (string): Path to log file (default: `.\work-sessions.json`)

**Health Checks:**
1. eva-orchestrator git status
2. eva-suite git status
3. Master plan validation
4. GitHub Pages deployment status
5. Marco Hub documentation presence

---

### 2. `logoff.ps1` - End Work Session
**Purpose:** Close active work session and calculate duration

**Usage:**
```powershell
.\logoff.ps1
.\logoff.ps1 -Notes "Completed feature X"
```

**Features:**
- Finds active session automatically
- Calculates duration in hours
- Prompts for optional notes
- Updates session status to "closed"
- Displays session summary

**Parameters:**
- `-Notes` (string): Optional session notes
- `-LogFile` (string): Path to log file (default: `.\work-sessions.json`)

---

### 3. `time-report.ps1` - Generate Time Report
**Purpose:** Generate summary report of work sessions

**Usage:**
```powershell
.\time-report.ps1
.\time-report.ps1 -Days 30
.\time-report.ps1 -Days 7 -Project "EVA Suite"
```

**Features:**
- Filter by date range (last N days)
- Filter by project name (partial match)
- Group by project with totals
- Group by day with totals
- Show recent sessions detail
- Export to CSV option

**Parameters:**
- `-Days` (int): Number of days to include (default: 7)
- `-Project` (string): Project name filter (optional)
- `-LogFile` (string): Path to log file (default: `.\work-sessions.json`)

---

### 4. `calculate-time.ps1` - Detailed Time Calculation
**Purpose:** Calculate total time with detailed breakdowns and analysis

**Usage:**
```powershell
.\calculate-time.ps1
.\calculate-time.ps1 -StartDate "2025-11-01" -EndDate "2025-11-30"
.\calculate-time.ps1 -Project "EVA Suite"
```

**Features:**
- Custom date range selection (interactive or parameters)
- Project selection (interactive or parameter)
- Breakdown by project with percentages
- Breakdown by week
- Detailed session list
- Average session duration
- Export to CSV or text summary

**Parameters:**
- `-StartDate` (string): Start date YYYY-MM-DD (optional, prompts if not provided)
- `-EndDate` (string): End date YYYY-MM-DD (optional, defaults to today)
- `-Project` (string): Exact project name filter (optional, interactive selection)
- `-LogFile` (string): Path to log file (default: `.\work-sessions.json`)

---

## 🔴 Missing Scripts (To Be Created)

### 5. `list-sessions.ps1` - Quick Session List
**Purpose:** Simple list of all sessions with filtering

**Requirements:**
- List all sessions (active and closed)
- Filter by status (active/closed/all)
- Filter by date range
- Filter by project
- Color-coded output (active=green, closed=gray)
- Summary counts and totals

**Usage:**
```powershell
.\list-sessions.ps1
.\list-sessions.ps1 -Status active
.\list-sessions.ps1 -Days 7
.\list-sessions.ps1 -Project "EVA Suite"
```

**Test Cases:**
1. List all sessions
2. List only active sessions
3. List only closed sessions
4. Filter by last 7 days
5. Filter by project name
6. Combine filters (active + project)

---

### 6. `edit-session.ps1` - Edit Session Details
**Purpose:** Modify existing session properties

**Requirements:**
- Find session by ID or select interactively
- Edit project name
- Edit task description
- Edit notes
- Edit start/end times (with recalculation of duration)
- Validate all changes

**Usage:**
```powershell
.\edit-session.ps1
.\edit-session.ps1 -SessionId "20251124-154326"
.\edit-session.ps1 -SessionId "20251124-154326" -Project "New Project"
```

**Test Cases:**
1. Interactive session selection
2. Edit project name
3. Edit task description
4. Add/edit notes
5. Adjust start time
6. Adjust end time (recalculate duration)
7. Edit multiple fields
8. Validate JSON integrity after edit

---

### 7. `delete-session.ps1` - Delete Session
**Purpose:** Remove a session from the log

**Requirements:**
- Find session by ID or select interactively
- Display session details before deletion
- Require confirmation
- Backup option before deletion
- Update totals/summary after deletion

**Usage:**
```powershell
.\delete-session.ps1
.\delete-session.ps1 -SessionId "20251124-154326"
.\delete-session.ps1 -SessionId "20251124-154326" -Force
```

**Test Cases:**
1. Interactive session selection
2. Delete by session ID
3. Confirmation prompt (cancel)
4. Confirmation prompt (proceed)
5. Force delete without confirmation
6. Backup created before deletion
7. Validate JSON integrity after deletion

---

### 8. `merge-sessions.ps1` - Merge Multiple Sessions
**Purpose:** Combine multiple sessions into one (for fixing split sessions)

**Requirements:**
- Select multiple sessions interactively
- Calculate total duration
- Combine tasks/notes
- Keep earliest start time
- Keep latest end time
- Confirm before merging

**Usage:**
```powershell
.\merge-sessions.ps1
.\merge-sessions.ps1 -SessionIds @("20251124-154326", "20251124-160000")
```

**Test Cases:**
1. Interactive multi-select
2. Merge two sessions
3. Merge three or more sessions
4. Verify duration calculation
5. Verify combined task description
6. Confirmation prompt
7. Validate JSON integrity after merge

---

### 9. `export-sessions.ps1` - Advanced Export
**Purpose:** Export sessions to various formats with templates

**Requirements:**
- Export to CSV
- Export to JSON (filtered subset)
- Export to Excel-compatible format
- Export to Markdown table
- Custom templates for client reports
- Filter by date range, project, status

**Usage:**
```powershell
.\export-sessions.ps1 -Format CSV
.\export-sessions.ps1 -Format Markdown -Days 30
.\export-sessions.ps1 -Format Excel -Project "EVA Suite" -StartDate "2025-11-01"
```

**Test Cases:**
1. Export all to CSV
2. Export filtered to CSV
3. Export to Markdown
4. Export to JSON
5. Apply date filter
6. Apply project filter
7. Custom output filename
8. Verify exported data accuracy

---

### 10. `fix-sessions.ps1` - Data Integrity Repair
**Purpose:** Fix common issues in session data

**Requirements:**
- Find and close unclosed sessions
- Recalculate all durations
- Validate timestamps
- Fix malformed entries
- Remove duplicates
- Backup before fixing
- Report all changes made

**Usage:**
```powershell
.\fix-sessions.ps1
.\fix-sessions.ps1 -CloseOpen
.\fix-sessions.ps1 -RecalculateDurations
.\fix-sessions.ps1 -ValidateAll
```

**Test Cases:**
1. Detect unclosed sessions
2. Auto-close with estimated end time
3. Recalculate all durations
4. Find invalid timestamps
5. Find duplicate sessions
6. Backup creation
7. Dry-run mode
8. Validate JSON structure

---

### 11. `session-stats.ps1` - Statistics and Analytics
**Purpose:** Generate advanced statistics and insights

**Requirements:**
- Total hours by month/week/day
- Average session length
- Longest/shortest sessions
- Most active projects
- Most active days of week
- Time distribution charts (ASCII)
- Productivity trends
- Comparison periods

**Usage:**
```powershell
.\session-stats.ps1
.\session-stats.ps1 -Period Month
.\session-stats.ps1 -Compare -Period1 "2025-11" -Period2 "2025-10"
```

**Test Cases:**
1. Monthly statistics
2. Weekly statistics
3. Daily statistics
4. Project rankings
5. Day-of-week analysis
6. Time distribution chart
7. Period comparison
8. Export stats to file

---

### 12. `backup-sessions.ps1` - Backup Management
**Purpose:** Create and manage backups of session data

**Requirements:**
- Create timestamped backup
- List all backups
- Restore from backup
- Delete old backups (keep last N)
- Verify backup integrity
- Auto-backup on critical operations

**Usage:**
```powershell
.\backup-sessions.ps1 -Create
.\backup-sessions.ps1 -List
.\backup-sessions.ps1 -Restore -BackupFile "work-sessions-20251124.json"
.\backup-sessions.ps1 -Cleanup -Keep 10
```

**Test Cases:**
1. Create backup
2. List all backups
3. Restore from backup
4. Verify restored data
5. Cleanup old backups
6. Preserve minimum count
7. Auto-backup trigger

---

## Data Model

### Session Object Structure

```json
{
  "sessionId": "20251124-154326",
  "startTime": "2025-11-24 15:43:26",
  "startTimestamp": "2025-11-24T15:43:26.000Z",
  "endTime": "2025-11-24 18:30:00",
  "endTimestamp": "2025-11-24T18:30:00.000Z",
  "durationHours": 2.78,
  "project": "EVA Suite",
  "task": "Implement feature X",
  "notes": "Completed successfully",
  "user": "marco",
  "computer": "DESKTOP-ABC123",
  "status": "closed"
}
```

### Status Values
- `active` - Session in progress
- `closed` - Normal completion
- `closed-auto` - Auto-closed by system

---

## Common Patterns

### Loading Sessions
```powershell
$sessions = Get-Content "work-sessions.json" -Raw | ConvertFrom-Json
if ($sessions -isnot [array]) {
    $sessions = @($sessions)
}
```

### Saving Sessions
```powershell
$sessions | ConvertTo-Json -Depth 10 | Set-Content "work-sessions.json"
```

### Filtering by Date
```powershell
$filtered = $sessions | Where-Object {
    $date = [DateTime]::Parse($_.startTime)
    $date -ge $startDate -and $date -le $endDate
}
```

### Calculating Duration
```powershell
$start = [DateTime]::Parse($session.startTime)
$end = [DateTime]::Parse($session.endTime)
$duration = ($end - $start).TotalHours
```

---

## Testing Checklist

For each new script, test:

1. ✅ **Normal operation** - Happy path works
2. ✅ **File not found** - Handles missing work-sessions.json
3. ✅ **Empty file** - Handles empty array
4. ✅ **Malformed JSON** - Error handling
5. ✅ **No sessions match filter** - Appropriate message
6. ✅ **Active session exists** - Proper handling
7. ✅ **Parameters** - All parameters work
8. ✅ **Interactive prompts** - User input validation
9. ✅ **Data integrity** - JSON remains valid
10. ✅ **Backup/restore** - Data can be recovered

---

## Development Priority

### Phase 1 (Immediate)
1. `list-sessions.ps1` - Most commonly needed
2. `fix-sessions.ps1` - Critical for data integrity

### Phase 2 (Short-term)
3. `edit-session.ps1` - Frequent need for corrections
4. `export-sessions.ps1` - Client reporting

### Phase 3 (Medium-term)
5. `session-stats.ps1` - Analytics and insights
6. `backup-sessions.ps1` - Data protection

### Phase 4 (Long-term)
7. `delete-session.ps1` - Less frequent need
8. `merge-sessions.ps1` - Edge case handling

---

## Lessons Learned

### ❌ Don't Do:
- Write inline PowerShell scripts in chat
- Recreate functionality that already exists
- Skip checking existing scripts first
- Ignore established patterns
- Create throw-away code

### ✅ Do:
- Check this catalog FIRST
- Use existing scripts as templates
- Follow established patterns
- Test thoroughly before declaring done
- Update this catalog when adding scripts
- Create reusable, maintainable code

---

## Quick Reference

**Start work:** `.\login.ps1`  
**End work:** `.\logoff.ps1`  
**Today's report:** `.\time-report.ps1 -Days 1`  
**Week report:** `.\time-report.ps1`  
**Month total:** `.\calculate-time.ps1 -StartDate "2025-11-01"`  
**List active:** `.\list-sessions.ps1 -Status active` (TODO)  
**Fix issues:** `.\fix-sessions.ps1` (TODO)

---

**End of Catalog**
