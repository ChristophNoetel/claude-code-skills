# Migration Guide: Basic to Obsidian

Already using the basic memory system (`.claude/memory/`) and want to move to Obsidian? Here's how.

## What you're moving

```
.claude/memory/
├── MEMORY.md           --> vault/Memory/MEMORY.md
└── memories/
    ├── *.md            --> vault/Memory/*.md (flattened, no subfolder)
```

## Steps

### 1. Create the vault structure

Follow the [setup guide](setup-guide.md) steps 1-2 to create your vault folders and add the CLAUDE.md config.

### 2. Move your memory files

```bash
# Copy index
cp .claude/memory/MEMORY.md /path/to/vault/Memory/MEMORY.md

# Copy individual memories (flatten into Memory/)
cp .claude/memory/memories/*.md /path/to/vault/Memory/
```

### 3. Update frontmatter

Your existing files have this frontmatter:

```yaml
---
name: some-name
description: ...
type: feedback
---
```

Add the new fields to each file:

```yaml
---
name: some-name
description: ...
type: feedback
author: claude
created: 2025-01-15
categories:
  - "[[Memory]]"
---
```

**Shortcut:** You don't have to do all files at once. Claude will add the new fields as it touches memories in future sessions. But add `author` and `created` to your most important feedback memories now.

### 4. Rename with semantic prefixes

If your files are named `example-*` or generic names, rename them with type prefixes:

| Type | Prefix | Example |
|------|--------|---------|
| feedback | `feedback_` | `feedback_no-db-mocks.md` |
| project | `project_` | `project_api-migration.md` |
| reference | `reference_` | `reference_monitoring.md` |
| user | (none) | `my-role.md` |

### 5. Convert links to wikilinks

In `MEMORY.md`, change markdown links:

```markdown
<!-- Before -->
- [No DB mocks](memories/feedback-no-db-mocks.md) -- integration tests use real DB

<!-- After -->
- [[feedback_no-db-mocks]] -- integration tests use real DB
```

### 6. Update CLAUDE.md

Replace the basic memory snippet with the [Obsidian snippet](claude-md-snippet.md). The key change is the vault path and the new authorship/locking rules.

### 7. Remove old location

Once you've verified everything works in Obsidian:

```bash
rm -rf .claude/memory/
```

Or move it somewhere as a backup first.

## What's different after migration

| Before (basic) | After (Obsidian) |
|----------------|-----------------|
| Files in `.claude/memory/memories/` | Files flat in `vault/Memory/` |
| Markdown links `[title](path)` | Wikilinks `[[file-name]]` |
| No authorship tracking | `author: claude/human/hybrid` on every note |
| No edit protection | `locked: true` blocks Claude edits |
| No daily workflow | `Daily/` with latest + ledger pattern |
| No todos | `Todos/` with weekly lists |

## Verify

Start a new Claude Code session and:

1. Ask Claude to recall something from memory -- it should read `Memory/MEMORY.md` from the vault path
2. Tell Claude a new preference -- it should create a new file in `Memory/` with full frontmatter
3. Check the MEMORY.md index -- the new entry should use wikilinks
