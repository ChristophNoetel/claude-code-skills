# CLAUDE.md Snippet (Obsidian)

Copy the section below into your `CLAUDE.md` (project or global). Replace `YOUR_VAULT_PATH` with the absolute path to your Obsidian vault.

---

```markdown
## Obsidian Vault (Persistent Memory)

**Vault path:** `YOUR_VAULT_PATH`

This vault is the single source of truth for persistent memory, todos, and work knowledge.
Memory index: `Memory/MEMORY.md` -- read this first, then follow wikilinks to specific files.

### Folder conventions

| Folder | Purpose |
|--------|---------|
| `Memory/` | Persistent memories (the core system) |
| `Daily/` | Daily notes, briefings, ledgers |
| `Todos/` | Active todo lists with `done/` archive |
| `Templates/` | Note templates (never edit existing ones without asking) |

### Memory types

| Type | What to store | Structure |
|------|--------------|-----------|
| **user** | Your role, preferences, expertise, how you like to work | Free-form |
| **feedback** | Corrections and confirmed approaches | Rule + **Why:** + **How to apply:** |
| **project** | Ongoing work context not derivable from code or git | Fact + **Why:** + **How to apply:** |
| **reference** | Pointers to external systems (dashboards, boards, channels) | URL/location + context |

### Saving a memory (two-step process)

**Step 1** -- Write the memory to its own file in `Memory/` with this frontmatter:

```yaml
---
name: kebab-case-name
description: One-line summary (used to decide relevance in future sessions)
type: user | feedback | project | reference
author: claude
created: YYYY-MM-DD
---
```

Use semantic naming prefixes: `feedback_*`, `project_*`, `reference_*` (user memories need no prefix).

**Step 2** -- Add a one-line pointer in `Memory/MEMORY.md`:
`- [[file-name]] -- short hook`

### Authorship rules (enforce on every note)

- Before editing any note: check for `locked: true` frontmatter -- if present, **skip it** and tell the user
- Creating a new note: set `author: claude`
- Editing a note with `author: human`: change to `author: hybrid`
- Editing a note with `author: claude`: keep `author: claude`

### Date discipline

- Use `YYYY-MM-DD` format everywhere
- Convert relative dates to absolute when saving (e.g., "next Thursday" -> "2026-06-05")
- This prevents memories from becoming uninterpretable after time passes

### Daily workflow (latest + ledger)

For recurring outputs (briefings, status reports, digests):
- Write to `Daily/*-latest.md` (overwritten each run)
- Append to `Daily/ledger/*-ledger.md` (historical archive)

### When to read memories

- When context from prior sessions seems relevant
- When the user asks you to recall, check, or remember something
- Before acting on a remembered fact, verify it's still current (memories go stale)

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
- Anything Obsidian's graph view already captures via wikilinks

### Keeping memory healthy

- Keep `Memory/MEMORY.md` under 200 lines
- Update or remove memories that are wrong or outdated
- Check for duplicates before writing a new memory
- Prefer updating existing memories over creating new ones on the same topic
```
