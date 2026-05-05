# Basic Memory Setup (Lite)

This is the quick-start option -- no external tools required. If you want the full-featured version with graph navigation, templates, and daily workflows, see the [Obsidian setup](../obsidian/setup-guide.md).

## Setup (5 minutes)

### Step 1: Copy this directory

```bash
# Project-level memory
cp -r basic/ .claude/memory/

# Or global memory
cp -r basic/ ~/.claude/memory/
```

### Step 2: Add the config to CLAUDE.md

Copy the snippet from [`claude-md-snippet.md`](claude-md-snippet.md) into your CLAUDE.md file. Update the path to match where you placed the files.

### Step 3: Start using it

Start a Claude Code session. Tell it about yourself and your project. Watch it create memory files.

### Step 4: Correct and confirm

When Claude gets something wrong, correct it. When it gets something right in a non-obvious way, confirm it. Both create feedback memories.

## What's included

```
basic/
├── MEMORY.md              # Index file (Claude reads this first)
├── claude-md-snippet.md   # Config to add to your CLAUDE.md
├── README.md              # This file
└── memories/
    ├── example-user-role.md
    ├── example-feedback-rule.md
    ├── example-project-context.md
    └── example-reference-tool.md
```

Delete the example files once you have real memories. They're there to show the format.

## Upgrading to Obsidian

When your memory grows beyond 10-15 files and you want better navigation, see the [migration guide](../obsidian/migration-guide.md).
