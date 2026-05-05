# How Claude Never Forgets

Every Claude Code session starts from scratch. The project context you explained yesterday? Gone. The preference you corrected three times? Forgotten.
This guide shows you how to give Claude Code a persistent memory that survives across sessions -- so corrections stick, context accumulates, and Claude gets better at working with you over time.

## The problem

Without persistent memory, you're training Claude from zero every session. You repeat the same corrections ("don't mock the database", "use snake_case for this project", "I'm the backend engineer, not the PM"). You re-explain the project context that took 10 minutes the first time. There's no learning curve -- Claude makes the same mistakes in session 100 that it made in session 1.

## The solution: file-based memory

Claude Code has a built-in memory feature that stores facts about you and your work in markdown files. But out of the box, it's a flat list of disconnected notes. This guide gives it structure.

The pattern is simple:

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

## Memory types

The system uses four types of memory, each with a specific purpose and structure.

### User memories

Store information about you -- your role, expertise, preferences, and how you like to work.

```yaml
---
name: user-role
description: My role, expertise, and how I like to work
type: user
---

Senior backend engineer, 5 years Python. New to this frontend codebase.
Prefer concise explanations with code examples over long prose.
```

**When to save:** When you tell Claude about yourself, your preferences, or your expertise level.

### Feedback memories

Store corrections and confirmed approaches. These are the most powerful memory type -- they turn a single correction into a permanent behavioral change.

```yaml
---
name: feedback-no-db-mocks
description: Integration tests must use real database, not mocks
type: feedback
---

Integration tests must hit a real database, not mocks.

**Why:** In Q4 2025, mocked tests passed but the production migration
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
name: project-api-migration
description: API v2 migration -- timeline, stakeholders, constraints
type: project
---

Migrating the public REST API from v1 to v2. Target: end of Q3.

**Why:** v1 has inconsistent error formats. Three enterprise customers
escalated. Product is committed to the fix in Q2.

**How to apply:** New endpoints use v2 patterns. Don't add features to v1.
```

**When to save:** When you learn about deadlines, stakeholder decisions, or project constraints that aren't documented in code.

### Reference memories

Store pointers to where information lives in external systems -- dashboards, Jira boards, Slack channels, documentation sites.

```yaml
---
name: reference-monitoring-dashboard
description: Production monitoring dashboard URL and what it tracks
type: reference
---

**URL:** https://grafana.example.com/d/api-latency

Primary oncall dashboard for API latency. If you're touching
request-handling code, this is what will page someone.
```

**When to save:** When you mention an external system and its purpose.

> The examples above are abbreviated. See [`template/memories/`](template/memories/) for full, copy-paste-ready versions of each type.

## The feedback loop

This is where the system gets interesting. Here's the lifecycle:

```
You correct Claude  -->  Claude saves a feedback memory
                              |
                              v
Future sessions read it  -->  Claude's behavior changes permanently
                              |
                              v
You never need to correct it again
```

**Concrete example:**

- **Session 1:** You say "don't mock the database in these tests -- we got burned last quarter when mocked tests passed but the prod migration failed."
- **Claude saves:** `feedback-no-db-mocks.md` with the rule, the reason, and when to apply it.
- **Session 47:** Claude is writing tests for a new endpoint. Without being told, it sets up the test database container and writes integration tests against real tables.

One correction. Permanent change.

This compounds. After a few weeks of normal use, you'll have 15-20 feedback memories. That's 15-20 things Claude gets right automatically that it used to get wrong. The more you correct, the better it gets -- and unlike a human teammate, it never backslides.

## Setting it up

### Step 1: Copy the template

Copy the [`template/`](template/) directory to wherever you want your memory to live:

```bash
# Example: project-level memory
cp -r template/ .claude/memory/

# Example: global memory
cp -r template/ ~/.claude/memory/
```

### Step 2: Add the CLAUDE.md config

Copy the config from [`claude-md-snippet.md`](claude-md-snippet.md) into your CLAUDE.md file. Update the path to point to where you placed the template.

### Step 3: Start using it

Start a Claude Code session and tell it about yourself and your project. Claude will create memory files and add them to the index.

### Step 4: Correct and confirm

When Claude gets something wrong, correct it. Watch it save a feedback memory. When it gets something right in a non-obvious way, say so -- that gets saved too.

### What to expect

- **Week 1:** A handful of memories. Claude starts remembering your role and basic preferences.
- **Week 2-3:** Feedback memories accumulate. You notice Claude avoiding mistakes it used to make.
- **Month 2+:** The memory system feels invisible. Claude just "knows" how you work. New sessions feel like picking up a conversation, not starting from scratch.

## What NOT to store

Memory should hold what can't be derived from the current state of the project:

- **Not code patterns or architecture** -- read the code. It changes; memories about it go stale.
- **Not git history** -- `git log` is authoritative. A memory about "recent changes" is outdated by tomorrow.
- **Not debugging solutions** -- the fix is in the code. The commit message has the context.
- **Not things already in CLAUDE.md** -- that's already loaded every session.

The test: if you can `grep` for it or `git log` it, don't memorize it.

## Tips

- **Keep the index under 200 lines.** Claude loads `MEMORY.md` every session. A sprawling index wastes context.
- **Update stale memories.** A "current project" memory from three months ago is noise. Review and prune periodically.
- **The Why line matters.** `Don't mock the DB` is a rule. `Don't mock the DB because mocks hid a broken migration last quarter` is a judgment tool. Claude can use the Why to decide edge cases.
- **Start small.** You don't need 50 memories on day one. Let the system grow naturally from real corrections and real context. The best memories come from actual friction, not from trying to pre-populate everything upfront.
