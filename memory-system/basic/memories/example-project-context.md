---
name: project-api-migration
description: API v2 migration -- timeline, stakeholders, constraints
type: project
---

# API v2 Migration

Migrating the public REST API from v1 to v2. Target completion: end of Q3. All new endpoints use v2; v1 stays live until Q4 for backward compatibility.

**Why:** v1 has inconsistent error formats and no pagination on list endpoints. Three enterprise customers escalated about it. Product committed to the fix in the Q2 roadmap.

**How to apply:** Any new endpoint work should use the v2 patterns (standardized error envelope, cursor-based pagination). Don't add features to v1 endpoints -- instead, flag them as candidates for v2 migration. When reviewing PRs, check that new routes register under `/api/v2/`.
