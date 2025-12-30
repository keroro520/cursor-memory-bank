# REPORT Command - Weekly Report Generation

This command generates structured weekly reports from Memory Bank data, providing a comprehensive summary of completed work, lessons learned, and progress tracking.

## Memory Bank Integration

Reads from:
- `memory-bank/archive/archive-*.md` - Completed tasks within the date range
- `memory-bank/reflection/reflection-*.md` - Task reflections and lessons learned
- `memory-bank/lessons/` - Lessons knowledge base (new lessons and applications)
- `memory-bank/tasks.md` - Currently ongoing tasks
- `memory-bank/progress.md` - Implementation progress details

Creates:
- `memory-bank/reports/weekly/YYYY-WNN.md` - Weekly report in ISO week format
- `memory-bank/reports/_index.md` - Report index (auto-updated)

Updates:
- `memory-bank/reports/_index.md` - Adds new report entry and updates statistics

## Progressive Rule Loading

### Step 1: Load Core Rules
```
Load: .cursor/rules/isolation_rules/main.mdc
Load: .cursor/rules/isolation_rules/Core/memory-bank-paths.mdc
```

### Step 2: Load Report Generation Rules
```
Load: .cursor/rules/isolation_rules/Core/report-generation.mdc
```

## Workflow

1. **Determine Date Range**
   - Default: Current week (Monday 00:00 to Sunday 23:59)
   - Parse optional parameters: `--weeks N` for last N weeks
   - Calculate ISO week number (e.g., 2024-W05)

2. **Verify Memory Bank Structure**
   - Check if `memory-bank/archive/` exists
   - Create `memory-bank/reports/` directory if needed
   - Create `memory-bank/reports/weekly/` subdirectory if needed

3. **Collect Archived Tasks**
   - Scan `memory-bank/archive/` directory for all archive files
   - For each archive file:
     - Read file content
     - Extract metadata (Task ID, Completion Date, Level)
     - Check if completion date falls within target date range
     - Parse task summary, requirements, and lessons
   - Filter and collect matching tasks

4. **Analyze Task Data**
   - Group tasks by complexity level (Level 1-4)
   - Count total tasks
   - Extract key achievements from each task
   - Identify challenges and solutions
   - Collect lessons learned sections

5. **Collect New Lessons**
   - Scan `memory-bank/lessons/` for lesson files
   - For each lesson in each module:
     - Check "Created" date in Effectiveness metadata
     - If created within date range, add to new lessons list
   - Group new lessons by module

6. **Analyze Lesson Usage**
   - From archived tasks, find "Relevant Lessons Referenced" sections
   - For each referenced lesson:
     - Track lesson ID and module
     - Count applications
     - Assess effectiveness (from reflection documents)
   - Calculate most applied lessons

7. **Read Ongoing Tasks**
   - Parse `memory-bank/tasks.md`
   - Identify tasks in progress (not yet archived)
   - Extract task name, level, and current phase
   - Estimate completion status if available

8. **Generate Report**
   - Use weekly report template
   - Populate all sections with collected data:
     - Weekly overview with statistics
     - Completed tasks list
     - New lessons and insights
     - Challenges and solutions
     - Lesson application tracking
     - Ongoing work status
     - Summary statistics
   - Format markdown with proper headers and lists

9. **Save Report**
   - Determine filename: `YYYY-WNN.md` (ISO week format)
   - Save to `memory-bank/reports/weekly/`
   - Update or create `memory-bank/reports/_index.md`
   - Add entry to index with summary statistics

10. **Display Summary**
    - Show report generation progress
    - Display key statistics
    - Print file path
    - Provide tips for next steps

## Usage

```bash
# Generate report for current week
/report

# Generate report for last 2 weeks (combined)
/report --weeks 2

# Generate report for specific date range (future enhancement)
# /report --start 2024-01-01 --end 2024-01-07
```

## Report Format

Reports use ISO 8601 week numbering:
- Week starts on Monday
- Format: `YYYY-WNN` where WNN is week number (01-53)
- Examples:
  - `2024-W01.md` = Week 1 of 2024 (Jan 1-7)
  - `2024-W05.md` = Week 5 of 2024 (Jan 29 - Feb 4)

## Output Example

```
📊 Generating Weekly Report...

Date Range: 2024-01-29 to 2024-02-04 (Week 5)

✓ Scanning archived tasks... found 8 tasks
  - Level 1: 2 tasks (25%)
  - Level 2: 3 tasks (37.5%)
  - Level 3: 2 tasks (25%)
  - Level 4: 1 task (12.5%)

✓ Extracting new lessons... found 3 lessons
  - authentication: 2 lessons
  - database: 1 lesson

✓ Analyzing lesson usage...
  - Referenced: 5 lessons
  - Applications: 8 times
  - Success rate: 87.5%

✓ Checking ongoing tasks... 2 tasks in progress

📝 Report generated successfully!

File: memory-bank/reports/weekly/2024-W05.md

📊 Weekly Summary:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Completed Tasks: 8
📚 New Lessons: 3
🎯 Key Achievement: User authentication system refactored
⏳ In Progress: 2 tasks
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

💡 Next Steps:
- Review the report: cat memory-bank/reports/weekly/2024-W05.md
- Share with team if needed
- Use insights for planning: /van [next task]
```

## Edge Cases

### No Archived Tasks
If no tasks completed in the date range:
```
⚠️ No completed tasks found for Week 5, 2024
No report generated.

💡 Tip: Complete and archive tasks using /archive before generating reports.
```

### First Report
If `reports/` directory doesn't exist:
- Automatically create directory structure
- Create `_index.md` with initial template
- Generate first report

### Missing Metadata
If archive files lack completion dates:
- Use file modification time as fallback
- Log warning about missing metadata
- Continue processing

## Integration with Workflow

```
Regular Development Workflow:
/van → /plan → /creative → /build → /reflect → /archive

Weekly Review (typically Friday afternoon):
/report

Next Week Planning:
Review report → Identify learnings → /van [next week's tasks]
```

## Next Steps

After generating a report:
- **Review**: Read the report for completeness and insights
- **Reflect**: Use insights to improve next week's planning
- **Share**: Share with team/manager if applicable
- **Plan**: Use learnings to inform next week's task prioritization
- **Archive**: Report is automatically archived in Memory Bank

## Future Enhancements

Phase 2 (planned):
- Custom date ranges (--start, --end parameters)
- Multiple output formats (--format brief|full|executive)
- Monthly report generation (--monthly flag)
- Comparison with previous weeks (--compare flag)

Phase 3 (future):
- Export to PDF/HTML
- Email delivery
- Trend visualization
- Team report aggregation
