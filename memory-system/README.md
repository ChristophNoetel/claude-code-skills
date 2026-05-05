# How Claude Never Forgets

Every Claude Code session starts from scratch. The project context you explained yesterday? Gone. The preference you corrected three times? Forgotten.

This guide gives Claude Code persistent memory that survives across sessions -- so corrections stick, context accumulates, and Claude gets better at working with you over time.

## Choose Your Path

| | Obsidian (Recommended) | Basic (Lite) |
|---|---|---|
| Best for | Daily users who want navigable, growing memory | Quick setup, no external tools |
| Features | Graph view, templates, daily notes, authorship tracking, edit locking | Index file + memory files |
| Setup time | ~15 min | ~5 min |
| Guide | [obsidian/setup-guide.md](obsidian/setup-guide.md) | [basic/README.md](basic/README.md) |

Already using the basic setup and want to upgrade? See the [migration guide](obsidian/migration-guide.md).

---

## The Problem

Without persistent memory, you're training Claude from zero every session. You repeat the same corrections ("don't mock the database", "use snake_case for this project", "I'm the backend engineer, not the PM"). You re-explain the project context that took 10 minutes the first time. There's no learning curve -- Claude makes the same mistakes in session 100 that it made in session 1.

## The Solution: File-Based Memory

Claude Code's memory stores facts about you and your work in markdown files. This guide gives it structure:

1. **An index file** (`MEMORY.md`) that Claude reads at the start of every session
2. **Individual memory files** with typed frontmatter, organized by purpose
3. **A CLAUDE.md config** that tells Claude when and how to read/write memories

Claude reads the index, follows links to relevant memories, and carries that context into the current session. When something new comes up -- a correction, a preference, a project update -- it writes a new memory file and adds it to the index.

### Why files beat conversation history

- **They survive sessions.** Conversation history is gone when you close the terminal.
- **They're searchable.** `grep` your memories. Try that with a chat log.
- **They're versionable.** Track memory changes in git.
- **They're editable.** Fix a wrong memory directly. No need to re-correct Claude.
- **They're portable.** Move them between machines, share them with a team.

## Memory Types

The system uses four types of memory, each with a specific purpose and structure.

### User memories

Store information about you -- your role, expertise, preferences, and how you like to work.

```yaml
---
name: user-role
description: My role, expertise, and how I like to work
type: user
author: claude
created: 2025-06-01
---

Senior backend engineer, 5 years Python. New to this frontend codebase.
Prefer concise explanations with code examples over long prose.
```

**When to save:** When you tell Claude about yourself, your preferences, or your expertise level.

### Feedback memories

Store corrections and confirmed approaches. These are the most powerful type -- they turn a single correction into a permanent behavioral change.

```yaml
---
name: feedback_no-db-mocks
description: Integration tests must use real database, not mocks
type: feedback
author: claude
created: 2025-06-01
---

Integration tests must hit a real database, not mocks.

**Why:** In Q4, mocked tests passed but the production migration
failed. The mock didn't enforce the NOT NULL constraint.

**How to apply:** When writing tests for DB-touching code, always spin up
the test database container. Only mock external HTTP services.
```

**When to save:** Any time you correct Claude's approach ("don't do that") OR confirm a non-obvious approach worked ("yes, exactly like that").

The `Why` and `How to apply` lines are critical. Without them, Claude blindly follows the rule. With them, it can judge edge cases.

### Project memories

Store ongoing work context that isn't in the code or git history -- the "why" behind current work, deadlines, stakeholder constraints.

```yaml
---
name: project_api-migration
description: API v2 migration -- timeline, stakeholders, constraints
type: project
author: claude
created: 2025-06-01
---

Migrating the public REST API from v1 to v2. Target: end of Q3.

**Why:** v1 has inconsistent error formats. Enterprise customers
escalated. Product committed to the fix.

**How to apply:** New endpoints use v2 patterns. Don't add features to v1.
```

**When to save:** When you learn about deadlines, stakeholder decisions, or project constraints that aren't documented in code.

### Reference memories

Store pointers to where information lives in external systems -- dashboards, Jira boards, Slack channels, documentation sites.

```yaml
---
name: reference_monitoring-dashboard
description: Production monitoring dashboard URL and what it tracks
type: reference
author: claude
created: 2025-06-01
---

**URL:** https://grafana.example.com/d/api-latency

Primary oncall dashboard for API latency. If you're touching
request-handling code, this is what will page someone.
```

**When to save:** When you mention an external system and its purpose.

## The Feedback Loop

This is where the system gets interesting:

```
You correct Claude  -->  Claude saves a feedback memory
                              |
                              v
Future sessions read it  -->  Claude's behavior changes permanently
                              |
                              v
You never need to correct it again
```

One correction. Permanent change. After a few weeks, you'll have 15-20 feedback memories -- things Claude gets right automatically that it used to get wrong. Unlike a human teammate, it never backslides.

## What NOT to Store

- **Code patterns or architecture** -- read the code. It changes; memories about it go stale.
- **Git history** -- `git log` is authoritative. A memory about "recent changes" is outdated by tomorrow.
- **Debugging solutions** -- the fix is in the code. The commit message has the context.
- **Things already in CLAUDE.md** -- that's already loaded every session.
- **Personal information about other people** -- memories are about your preferences and your environment.

The test: if you can `grep` for it or `git log` it, don't memorize it.

## Tips

- **Keep the index under 200 lines.** Claude loads `MEMORY.md` every session. A sprawling index wastes context.
- **Use semantic naming.** `feedback_no-db-mocks.md` tells you what it is at a glance. `memory-7.md` doesn't.
- **Update stale memories.** A "current project" memory from three months ago is noise. Review and prune periodically.
- **The Why line matters.** `Don't mock the DB` is a rule. `Don't mock the DB because mocks hid a broken migration` is a judgment tool.
- **Start small.** Let the system grow from real corrections and real context. The best memories come from actual friction, not pre-population.
- **Verify before acting.** Memories go stale. Before acting on a remembered fact, Claude should check that it's still true.
