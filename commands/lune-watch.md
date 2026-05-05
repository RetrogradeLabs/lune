---
name: lune-watch
description: Subscribe to a conference and drain new papers
arguments:
  - name: conference
    description: Conference short name (e.g. CCS, NeurIPS)
    required: true
  - name: topics
    description: Comma-separated topics (optional)
---

Use the `lune-conference-monitor` skill:
1. Call `list_subscriptions` to check if {{conference}} is already tracked.
2. If not, call `create_subscription(conference="{{conference}}", topics=[...{{topics}}])`.
3. Then call `drain_subscription` and surface the new papers in a compact table.
