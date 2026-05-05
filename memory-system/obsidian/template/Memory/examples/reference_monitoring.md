---
name: reference_monitoring
description: Production monitoring dashboard URL and what it tracks
type: reference
author: claude
created: 2025-06-01
categories:
  - "[[Memory]]"
---

**URL:** https://grafana.example.com/d/api-latency

Primary oncall dashboard for API latency. If you're touching request-handling code, this is what will page someone.

Check this dashboard before and after deploying changes to request middleware.
