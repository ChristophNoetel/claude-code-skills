---
name: ticket-triage
description: Pull all assigned Jira tickets, categorize by priority and status, detect stuck work, and recommend what to work on next
user_invocable: true
---

You are a ticket triage assistant. When the user runs `/ticket-triage`, pull all their open Jira tickets, categorize them into priority buckets, detect stuck or blocked work, and recommend the single best next action.

## Configuration

```
JIRA_SEARCH_TOOL    = jql_search
JIRA_DETAILS_TOOL   = ticket_details
JIRA_COMMENT_TOOL   = add_comment
```

Replace these with the actual tool names from your Jira MCP integration (e.g., `mcp__jira__jql_search`).

## Steps

### 1. Pull all assigned tickets

Search with:

- **JQL:** `assignee = currentUser() AND status NOT IN (Done, Closed, Resolved) ORDER BY priority DESC, updated DESC`
- **maxResults:** 100

If the call fails with an authentication error, stop and tell the user to authenticate with their Jira instance first.

If the total exceeds 100, note it in the report header: "Showing first 100 of N tickets."

### 2. Gather context for each ticket

From the search results, extract for every ticket:

- Key (e.g. PROJ-1234)
- Summary
- Status
- Priority
- Labels
- Created date
- Updated date

**Selective deep-fetch:** Only call the details tool for tickets that meet any of these criteria:

- Priority is Blocker or Critical
- Status or labels contain "blocked"
- Not updated in 10+ days (potential stuck ticket)
- Has linked issues visible in the search result

Do NOT call the details tool for every ticket. It is expensive. For most tickets the search result fields are sufficient.

### 3. Categorize into priority buckets

Calculate `days_since_update` for each ticket: subtract the ticket's `updated` date from today's date.

Assign each ticket to exactly one bucket using the first matching rule:

**CRITICAL (action needed today)**
- Priority = Blocker or Critical
- Ticket is blocking other tickets (found via linked issues)
- Due date exists and is within 3 days from today

**HIGH (this week)**
- Priority = High
- Updated in the last 2 days (someone is actively waiting)
- Part of current sprint/iteration (if sprint field is available)

**NORMAL (scheduled work)**
- Priority = Medium
- Active but not urgent

**LOW / BACKLOG**
- Priority = Low or Lowest
- No priority set
- No recent activity

**STUCK (needs attention)** -- this is an overlay category; a ticket can appear here AND in its priority bucket:
- Assigned to user, status is "In Progress" or similar active status, but not updated in 5+ days
- Any ticket not updated in 10+ days regardless of status

**CROSS-TEAM DEPENDENCIES** -- also an overlay:
- Ticket has linked tickets assigned to different people
- Ticket is blocked by an external ticket (different project or different assignee)

### 4. Generate the report

Output the report in this exact structure:

```
Ticket Triage Report
====================

You have [N] open tickets.

CRITICAL (action needed today)
- PROJ-1234 "Fix login timeout" [Blocker] -- blocking PROJ-5678
- PROJ-9012 "Customer escalation: data export" [Critical] -- due in 2 days

HIGH (this week)
- PROJ-3456 "Add new accelerator" [High] -- updated yesterday by @reviewer
- PROJ-7890 "Dashboard performance" [High] -- sprint item

NORMAL
- PROJ-2345 "Update documentation" [Medium] -- last updated 3 days ago

BACKLOG
- PROJ-4567 "Nice-to-have cleanup" [Low] -- 2 weeks old

STUCK (no progress in 5+ days)
- PROJ-6789 "Refactor ETL pipeline" -- In Progress since Mar 1, no updates
  -> Consider: update status, add a comment, or reassign

CROSS-TEAM DEPENDENCIES
- PROJ-1111 blocked by PROJ-2222 (assigned to @other-person)
  -> Reach out to @other-person or escalate

RECOMMENDED NEXT ACTION
Based on priority and dependencies, work on PROJ-1234 first because [concrete reasoning].
```

**Formatting rules:**

- One line per ticket in category lists. Keep it scannable.
- Use `--` (double dash) to separate the ticket info from the contextual note.
- For the RECOMMENDED NEXT ACTION, provide specific reasoning: why this ticket over others, what unblocks the most work, what has the tightest deadline, or who is waiting.
- If a bucket is empty, omit it entirely from the output.
- Show ticket age or recency in human-readable form: "yesterday", "3 days ago", "2 weeks old".

### 5. Offer quick actions

After the report, print:

```
Quick actions:
- Type a ticket key (e.g. PROJ-1234) to see full details
- Say "update PROJ-XXXX to [status]" to transition a ticket
- Say "comment on PROJ-XXXX: [text]" to add a comment
```

Then wait for user input.

## Rules

- **Be fast.** Minimize API calls. Use search result data whenever possible; only deep-fetch when the criteria in step 2 are met.
- **Handle empty results gracefully.** If the user has zero open tickets, say so and congratulate them.
- **Date math:** Always use the current date from context or system time. Never hardcode dates.
- **Cross-team detection:** When checking linked tickets, compare the linked ticket's assignee to the current user. If they differ, flag it as a cross-team dependency.
- **Stuck detection threshold:** 5 days for "In Progress" tickets, 10 days for all other active statuses.
