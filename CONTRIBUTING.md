# Contributing

Thanks for your interest in contributing skills! Here's how to add one.

## Submitting a skill

1. Fork this repo
2. Add your skill as a single `.md` file in `skills/` (or a directory `skills/my-skill/SKILL.md` for skills with supporting files)
3. Open a pull request

## Skill format

Every skill needs YAML frontmatter:

```yaml
---
name: my-skill
description: One-line description of what it does
user_invocable: true
---
```

Then structure the body as:

1. **Context** -- what the skill does and when to use it
2. **Configuration** -- paths and constants the user should customize
3. **Steps** -- numbered workflow for Claude to follow
4. **Rules** -- guardrails and edge cases

See any existing skill in `skills/` for a working example.

## Before submitting

Run this sanitization check on your skill file:

```bash
grep -ri "internal-hostname\|/Users/yourname\|api-key\|password\|secret" skills/your-skill.md
```

Make sure there are no:
- Hardcoded absolute paths (use `~/` placeholders with a Configuration section)
- Internal hostnames, API keys, or credentials
- References to specific employers or proprietary systems
- Tool names tied to a specific MCP integration (use generic names with a Configuration section)

## Style

- Use `--` (double dash) instead of em dashes
- Use straight quotes, not smart quotes
- Keep skills self-contained -- one file should have everything Claude needs
- Prefer concrete examples over abstract descriptions
