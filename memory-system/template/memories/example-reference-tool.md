---
name: reference-monitoring-dashboard
description: Production monitoring dashboard URL and what it tracks
type: reference
---

# Production Monitoring Dashboard

**URL:** `https://grafana.example.com/d/api-latency`

The primary oncall dashboard for API latency. If you're touching request-handling code, this is what will page someone at 3am.

Tracks: p50/p95/p99 response times, error rate by endpoint, database query duration, and queue depth for async jobs.

The "Billing Webhooks" panel is the one that matters most -- it has a 500ms SLA and alerts if p95 exceeds it for 5 minutes.
