# Backlog Maintenance Standard

**Keep backlogs lean and actionable across all projects.**

## Problem

Backlogs grow stale over time:

- Completed items clutter the view
- Verbose analyses make scanning difficult
- File size impacts load times and searchability
- Hard to see what's actually ready to work

## Solution

Regular backlog cleanup following this standard:

---

## Cleanup Checklist (Run Monthly)

### 1. Archive Completed Items (5 min)

**Create/Update Archive:**

```bash
# If BACKLOG-ARCHIVE.md doesn't exist, create it
cat > docs/BACKLOG-ARCHIVE.md << 'ARCHIVE'
# Backlog Archive - Completed Items

Archive of completed backlog items. Updated: $(date +%Y-%m-%d)

## Recently Completed

| ID | Item | Type | Completed |
|----|------|------|-----------|
ARCHIVE

# Extract all ✅ Done items from tables
grep "✅" BACKLOG.md >> docs/BACKLOG-ARCHIVE.md
```

**Update Main Backlog:**

- Remove all ✅ Done rows from tables
- Replace "## Completed" section with:

  ```markdown
  ## Completed Items

  See [BACKLOG-ARCHIVE.md](./docs/BACKLOG-ARCHIVE.md) for all completed items.

  **Recent Completions:**

  - B-XXX: Description (Date)
  - B-XXX: Description (Date)
  - B-XXX: Description (Date)

  **Archive contains N+ completed items**
  ```

### 2. Extract Verbose Analyses (10 min)

**Identify verbose analyses:**

- Any analysis section >100 lines
- Detailed implementation plans
- Research findings

**Move to separate files:**

```markdown
# In BACKLOG.md, replace verbose section with:

**B-XXX Analysis:**

**Quick Summary:** 2-3 sentence overview

**Full Analysis:** See `docs/backlog-items/B-XXX-analysis.md`
```

**Create separate file:**

```bash
# Move content to docs/backlog-items/B-XXX-analysis.md
# Keep full details there
```

### 3. Verify Size Targets (2 min)

**Check file size:**

```bash
wc -l BACKLOG.md && du -h BACKLOG.md
```

**Targets:**

- ✅ Healthy: <1,500 lines, <75KB
- ⚠️ Warning: 1,500-2,500 lines, 75-120KB
- ❌ Bloated: >2,500 lines, >120KB

**If bloated:**

- Move more analyses to separate files
- Archive older "Backlog" items (>6 months old, low priority)
- Consolidate related items

### 4. Update Status Counts (1 min)

At top of BACKLOG.md, update:

```markdown
**Active Items:**

- Ready: X
- Backlog: Y
- Waiting: Z
- Total Active: N

**Archive:** M+ completed items (see BACKLOG-ARCHIVE.md)
```

---

## Automation (Future)

Add to package.json scripts:

```json
{
  "scripts": {
    "backlog:check": "node scripts/check-backlog-size.js",
    "backlog:archive": "node scripts/archive-completed.js",
    "backlog:clean": "npm run backlog:archive && npm run backlog:check"
  }
}
```

**Pre-commit hook:**

```bash
# .husky/pre-commit
if [ -f BACKLOG.md ]; then
  npm run backlog:check
fi
```

---

## Examples

### Good Backlog Structure

```markdown
# Project Backlog

**Active Items:** 25 Ready, 15 Backlog, 5 Waiting = 45 Total
**Archive:** 120+ completed items (see BACKLOG-ARCHIVE.md)

## 🔥 High Value - Next Up

| ID    | Feature           | Value              | Effort | Score | Status |
| ----- | ----------------- | ------------------ | ------ | ----- | ------ |
| B-240 | System assessment | Rev:5 Ret:5 Diff:5 | S      | 15.0  | Ready  |
| B-239 | CLI review        | Rev:4 Ret:5 Diff:5 | M      | 7.0   | Ready  |

**B-240 Analysis:**

Quick summary in 2-3 sentences.

**Full details:** See `docs/backlog-items/B-240-implementation-guide.md`

---

## Completed Items

See [BACKLOG-ARCHIVE.md](./docs/BACKLOG-ARCHIVE.md) for all completed items.

**Recent:** B-237 (Jan 17), B-206 (Jan 16), B-209 (Jan 14)
**Total:** 120+ archived
```

### Bad Backlog Structure (Avoid)

```markdown
# Project Backlog

## High Value

| ID    | Feature           | Status  |
| ----- | ----------------- | ------- | --------------------- |
| B-240 | System assessment | Ready   |
| B-165 | Architecture gate | ✅ Done | ❌ Clutters view      |
| B-166 | JSON output       | ✅ Done | ❌ Remove completed   |
| B-167 | Missing deps      | ✅ Done | ❌ Should be archived |

**B-240 Analysis:**

[500 lines of detailed analysis] ❌ Too verbose - move to separate file

**B-165 Completed Implementation:** ❌ Move to archive

[200 lines of what we did]

**B-166 Completed Implementation:** ❌ Move to archive

[150 lines of what we did]
```

---

## Benefits

1. **Faster scanning** - Only see active work
2. **Smaller files** - Better performance
3. **Historical reference** - Archive preserves context
4. **Clearer priorities** - Focus on what's next
5. **Standardized** - All projects follow same pattern

---

## When to Clean

**Monthly:** Routine maintenance
**Before big push:** Starting new phase, preparing for milestone
**When bloated:** File size >120KB or >2,500 lines
**After completion spike:** Just finished 10+ items

---

## Project-Specific Notes

Add project-specific guidelines here:

- Archive threshold (e.g., "Move items >6 months old to archive")
- Stakeholder preferences (e.g., "Keep last 3 months visible")
- Custom statuses (e.g., "Deferred", "On Hold")
