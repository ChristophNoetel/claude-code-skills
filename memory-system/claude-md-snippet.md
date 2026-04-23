# CLAUDE.md Memory Snippet

Copy the section below into your `CLAUDE.md` (or `~/.claude/CLAUDE.md` for global). Update the path to match where you placed the `template/` directory.

---

```markdown
## Persistent Memory

You have a file-based memory system at `<path-to-memory>/`. The index file is `MEMORY.md` -- read it first when you need context from prior sessions.

<!-- Replace <path-to-memory> with your actual path, e.g. ./.claude/memory or ./memory -->

### Memory types

| Type | What to store | Structure |
|------|--------------|-----------|
| **user** | Role, preferences, expertise, how I like to work | Free-form |
| **feedback** | Corrections and confirmed approaches | Rule + **Why:** + **How to apply:** |
| **project** | Ongoing work context not derivable from code or git | Fact + **Why:** + **How to apply:** |
| **reference** | Pointers to external systems (dashboards, boards, channels) | URL/location + context |

### Saving a memory

1. Write the memory to its own file in `<path-to-memory>/memories/` with this frontmatter:

   ```yaml
   ---
   name: kebab-case-name
   description: One-line summary (used to decide relevance in future sessions)
   type: user | feedback | project | reference
   ---
   ```

2. Add a one-line pointer in `<path-to-memory>/MEMORY.md`: `- [Title](memories/file.md) -- short hook`

### When to read memories

- When context from prior sessions seems relevant
- When the user asks you to recall, check, or remember something
- Before acting on a remembered fact, verify it's still current

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

### Keeping memory healthy

- Keep `MEMORY.md` under 200 lines
- Update or remove memories that are wrong or outdated
- Check for duplicates before writing a new memory
- Convert relative dates to absolute dates when saving (e.g., "Thursday" -> "2026-06-05")
```
