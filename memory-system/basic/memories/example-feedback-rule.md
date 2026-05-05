---
name: feedback-no-db-mocks
description: Integration tests must use real database, not mocks
type: feedback
---

# No Database Mocks in Integration Tests

Integration tests must hit a real database, not mocks.

**Why:** In Q4 2025, mocked tests passed but the production migration failed. The mock didn't enforce the NOT NULL constraint that the real schema had. We shipped a broken migration to staging and lost two days debugging.

**How to apply:** When writing tests for any code that touches the database, always spin up the test database container. Use `conftest.py` fixtures for test data setup. Only mock external HTTP services, never the database layer.
