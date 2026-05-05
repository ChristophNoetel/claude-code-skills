# Obsidian Memory System Setup

This is the recommended way to run Claude Code's persistent memory. Instead of isolated markdown files in `.claude/memory/`, your memories live inside an Obsidian vault -- giving you graph navigation, backlinks, templates, and a unified workspace for both human and AI knowledge.

## Why Obsidian?

Plain markdown files work. But they don't _compound_. Obsidian adds:

- **Graph view** -- see how memories, projects, and feedback rules connect
- **Backlinks** -- discover which memories reference each other without manual linking
- **Dataview queries** -- surface stale memories, count feedback rules by domain, find orphans
- **Templates** -- enforce consistent structure when creating new memories
- **Daily notes** -- natural integration point for morning briefings, daily logs, and status updates
- **Search** -- full-text search across all memories, todos, and notes simultaneously

After a month of use, your vault becomes a navigable knowledge base that both you and Claude can work with -- not just a flat pile of markdown.

## Prerequisites

1. **Obsidian** -- download from [obsidian.md](https://obsidian.md/)
2. **A vault** -- create a new vault or use an existing one
3. **Recommended plugins:**
   - [Templater](https://github.com/SilentVoid13/Templater) -- for memory file templates
   - [Dataview](https://github.com/blacksmithgu/obsidian-dataview) -- for querying memory health

## Setup (15 minutes)

### Step 1: Create the folder structure

Copy the [`template/`](template/) directory into your vault:

```bash
cp -r template/* /path/to/your/vault/
```

This creates:

```
your-vault/
├── Memory/
│   ├── MEMORY.md          # Central index (Claude reads this first)
│   ├── feedback-index.md  # Sub-index of behavioral rules
│   └── examples/          # Starter examples (delete after you have real ones)
├── Daily/
│   ├── briefing-latest.md
│   └── ledger/            # Historical archive
├── Todos/
│   ├── example-weekly-todo.md
│   └── done/              # Completed items
└── Templates/
    ├── Memory Template.md
    └── Daily Note Template.md
```

### Step 2: Add the CLAUDE.md config

Copy the snippet from [`claude-md-snippet.md`](claude-md-snippet.md) into your CLAUDE.md file (either project-level `.claude/CLAUDE.md` or global `~/.claude/CLAUDE.md`).

Update the vault path placeholder to your actual vault location.

### Step 3: Configure authorship rules

The Obsidian system tracks who wrote each note. This is enforced via the CLAUDE.md snippet -- Claude will:

- Set `author: claude` on notes it creates
- Change `author: human` to `author: hybrid` when it edits your notes
- Never edit notes with `locked: true` in frontmatter

### Step 4: Verify it works

Start a new Claude Code session and ask it to recall something from memory. It should:

1. Read `Memory/MEMORY.md`
2. Follow links to relevant memory files
3. Use the context in its response

Then tell Claude something about your preferences. It should create a new memory file in `Memory/` with proper frontmatter and add it to the index.

## How it works day-to-day

```
Session starts
    |
    v
Claude reads Memory/MEMORY.md (the index)
    |
    v
Follows wikilinks to relevant memories
    |
    v
Works with full context from prior sessions
    |
    +--- You correct Claude --> saves feedback memory
    |
    +--- You mention a project --> saves project memory
    |
    +--- You share a preference --> saves user memory
    |
    v
Index updated, vault grows organically
```

### The latest + ledger pattern (Daily/)

For recurring outputs (morning briefings, board status, daily digests):

- `*-latest.md` -- always holds the most recent version (overwritten each run)
- `ledger/*-ledger.md` -- appends each entry for historical reference

This gives you both "what's current?" (read `-latest`) and "what happened over time?" (read the ledger).

### Weekly todos (Todos/)

One active todo file per week. Completed items move to `done/`. This keeps your active surface small and scannable.

## Privacy

Never store personal information about other people in memory files. Memories are about _your_ preferences, _your_ projects, _your_ environment. If a project involves other people, reference their role generically ("the tech lead approved this") rather than storing identifying details.

## Skills co-location (optional)

Claude Code skills (`~/.claude/skills/`) can optionally live inside your vault via a symlink or NTFS junction, making them discoverable alongside your memory. See the [Claude Code docs](https://docs.anthropic.com/en/docs/claude-code) for details on skill configuration.

## What to expect

- **Week 1:** A handful of memories. Claude starts remembering your role and basic preferences.
- **Week 2-3:** Feedback memories accumulate. You notice Claude avoiding mistakes it used to make. Graph view starts showing clusters.
- **Month 2+:** The system feels invisible. New sessions feel like resuming a conversation. Obsidian's graph shows clear domains of knowledge.
