---
name: feedback_testing-style
description: Integration tests must use real database, not mocks
type: feedback
author: claude
created: 2025-06-01
categories:
  - "[[Memory]]"
---

Integration tests must hit a real database, not mocks.

**Why:** Mocked tests passed but the production migration failed -- the mock didn't enforce a NOT NULL constraint that the real DB did.

**How to apply:** When writing tests for DB-touching code, spin up the test database container. Only mock external HTTP services, not your own data layer.
