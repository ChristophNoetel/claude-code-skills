# CLAUDE.md Memory Snippet (Basic)

Copy the section below into your `CLAUDE.md` (or `~/.claude/CLAUDE.md` for global). Update the path to match where you placed the `basic/` directory.

For the full-featured Obsidian version with authorship tracking, daily workflows, and edit locking, see [`../obsidian/claude-md-snippet.md`](../obsidian/claude-md-snippet.md).

---

```markdown
## Persistent Memory

You have a file-based memory system at `<path-to-memory>/`. The index file is `MEMORY.md` -- read it first when you need context from prior sessions.

<!-- Replace <path-to-memory> with your actual path, e.g. ./.claude/memory or ~/.claude/memory -->

### Memory types

| Type | What to store | Naming prefix | Structure |
|------|--------------|---------------|-----------|
| **user** | Role, preferences, expertise, how I like to work | (none) | Free-form |
| **feedback** | Corrections and confirmed approaches | `feedback_` | Rule + **Why:** + **How to apply:** |
| **project** | Ongoing work context not derivable from code or git | `project_` | Fact + **Why:** + **How to apply:** |
| **reference** | Pointers to external systems (dashboards, boards, channels) | `reference_` | URL/location + context |

### Saving a memory

1. Write the memory to its own file in `<path-to-memory>/memories/` with this frontmatter:

   ```yaml
   ---
   name: kebab-case-name
   description: One-line summary (used to decide relevance in future sessions)
   type: user | feedback | project | reference
   author: claude
   created: YYYY-MM-DD
   ---
   ```

2. Add a one-line pointer in `<path-to-memory>/MEMORY.md`: `- [Title](memories/file.md) -- short hook`

### Authorship tracking

- Set `author: claude` on notes you create
- If the user manually edits a memory, respect their version as authoritative
- Check for `locked: true` in frontmatter -- if present, never edit that file

### Date discipline

- Use `YYYY-MM-DD` format for all dates
- Convert relative dates to absolute when saving (e.g., "next Thursday" -> "2026-06-05")
- This prevents memories from becoming uninterpretable after time passes

### When to read memories

- When context from prior sessions seems relevant
- When the user asks you to recall, check, or remember something
- Before acting on a remembered fact, verify it's still current (memories can go stale)

### When to save memories

- **Feedback**: Any time the user corrects your approach OR confirms a non-obvious approach worked
- **User**: When you learn about the user's role, preferences, or expertise
- **Project**: When you learn who is doing what, why, or by when
- **Reference**: When you learn about resources in external systems

### What NOT to save

- Code patterns or architecture (read the code -- it changes)
- Git history (use `git log`)
- Debugging solutions (they're in the commits)
- Anything already in this CLAUDE.md file
- Ephemeral task details from the current conversation
- Personal information about other people (names, roles, preferences of others)

### Keeping memory healthy

- Keep `MEMORY.md` under 200 lines
- Update or remove memories that are wrong or outdated
- Check for duplicates before writing a new memory
- Once you have 10+ feedback memories, consider grouping them in a `feedback-index.md`
```
