---
name: project_api-migration
description: API v2 migration -- timeline, stakeholders, constraints
type: project
author: claude
created: 2025-06-01
categories:
  - "[[Memory]]"
---

Migrating the public REST API from v1 to v2. Target: end of Q3 2025.

**Why:** v1 has inconsistent error formats. Enterprise customers escalated. Product committed to the fix.

**How to apply:** New endpoints use v2 patterns. Don't add features to v1. PRs touching API routes should follow the v2 response schema.
