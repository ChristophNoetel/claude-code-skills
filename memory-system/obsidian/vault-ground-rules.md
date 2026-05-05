# Vault Ground Rules

These conventions keep your vault navigable for both you and Claude. Copy this file into your vault as `Notes/Vault Ground Rules.md` and set `locked: true` in frontmatter so Claude doesn't modify it.

---

## The 8 Principles

### 1. One vault only

Keep work knowledge and Claude memory in the same vault. Splitting them creates two sources of truth that drift apart.

### 2. Flat structure

Don't nest folders deeply. Use frontmatter `categories` to organize instead:

```yaml
categories:
  - "[[Memory]]"
  - "[[Projects]]"
```

Allowed top-level folders: Memory/, Daily/, Todos/, Templates/, Notes/, References/, Attachments/. Everything else lives at root or in one of these.

### 3. Standard markdown

No proprietary syntax that breaks parsing outside Obsidian. Claude reads raw markdown files -- keep them interoperable.

Exceptions: wikilinks (`[[note]]`) are fine because they degrade gracefully to plain text.

### 4. Wikilinks for relationships

Use `[[wikilinks]]` liberally. They enable graph navigation and backlink discovery. A memory that mentions a project should link to it: `Working on [[API Migration]]`.

### 5. ISO dates everywhere

Use `YYYY-MM-DD` for all dates. In frontmatter, in note bodies, in file names. Never use relative dates in stored content -- "next week" is meaningless three months later.

### 6. Single weekly todo

One active todo note per week in `Todos/`. Completed items move to `Todos/done/`. This keeps your working surface small and prevents todo sprawl across dozens of files.

### 7. Authorship tracking (mandatory)

Every note must have an `author` field in frontmatter:

| Value | Meaning |
|-------|---------|
| `human` | You created it |
| `claude` | Claude created it autonomously |
| `hybrid` | Both have edited it |

This lets you filter the graph by authorship and know at a glance who wrote what.

### 8. Locking for ground truth

Add `locked: true` to frontmatter for notes that Claude must never edit -- vault ground rules, certain reference docs, personal ground truths. Claude should skip these and tell you if it encounters them.

---

## Recommended frontmatter template

```yaml
---
name: kebab-case-identifier
description: One-line summary
type: user | feedback | project | reference
author: claude | human | hybrid
created: YYYY-MM-DD
updated: YYYY-MM-DD
locked: false
categories:
  - "[[Memory]]"
---
```

Not all fields are required for every note. At minimum: `author` and `created`.
