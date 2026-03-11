# SaaS Playbook — Agent Instructions

## Project Structure

Flat repo — all `.md` files live at the root. No subdirectories, no nested structure.

```
saas-playbook/
  BACKLOG.md
  BACKLOG-MAINTENANCE.md
  LAUNCH-TEMPLATES.md
  QUALITY-GATES.md
  README.md
  REQUIREMENTS-ENGINEERING-STANDARD.md
  SOP-output-consistency.md
  TESTING-STRATEGY.md
```

## Validation Commands

Lint Markdown formatting:

```bash
npx markdownlint-cli2 "*.md"
```

Check for broken cross-references between docs:

```bash
rg '\[.*?\]\(([-\w]+\.md)\)' --only-matching --no-filename | sort -u | while read -r link; do
  file=$(echo "$link" | sed 's/.*(\(.*\))/\1/')
  [ ! -f "$file" ] && echo "Broken link: $link"
done
```

## Style Conventions

- **Headings:** `#` for document title, `##` for major sections, `###` for subsections. No skipping levels.
- **Tables:** Use GFM pipe tables with alignment. Always include a header row.
- **Lists:** Use `-` for unordered, `1.` for ordered. Indent nested lists with 2 spaces.
- **Code blocks:** Use fenced blocks with language identifier (e.g., ` ```bash `).
- **Links:** Relative paths for internal docs (`[Quality Gates](QUALITY-GATES.md)`), full URLs for external.
- **Line length:** No hard wrap. One sentence per line is preferred for clean diffs.
- **Tone:** Direct and actionable. Write for an engineer who needs to apply this today.

## Testing

No automated tests — this is a documentation-only repo.

Manual review checklist:
- [ ] Cross-references between docs resolve to existing files
- [ ] Tables render correctly in GitHub preview
- [ ] Heading hierarchy is consistent (no skipped levels)
- [ ] No orphaned docs (every `.md` except LICENSE is linked from README.md)
