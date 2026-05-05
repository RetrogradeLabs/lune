---
name: lune-conference-monitor
description: Use when the user wants to track new papers from specific conferences over time — "let me know when new CCS papers about side-channel attacks land", "monitor NeurIPS for diffusion-model work". Subscriptions are mailbox-style: create one, then drain it periodically with a cursor.
---

# Tracking conferences over time

Subscriptions are persistent server-side filters owned by the user's org. Create them with `create_subscription`, drain them with `drain_subscription(id, since=cursor)`.

## Steady-state pattern

```
1. List user's existing subs:           list_subscriptions
2. If they want a new topic, create:    create_subscription(conference="CCS", topics=["side-channel"])
3. Periodically (or on-demand):         drain_subscription(id, since=cursor)
                                        → returns { papers: [...], next_cursor: "..." }
4. Persist next_cursor for the next call (in your own session memory).
```

## When the user is new to subscriptions

If they say "watch CCS for me" without specifying topics/keywords, create the subscription with `conference` only — they'll get every new CCS paper. Confirm before creating.

## Don't

- Don't drain without a `since` cursor unless the user explicitly wants a fresh start. Without `since`, you may surface papers they've already seen.
- Don't create duplicate subscriptions; check `list_subscriptions` first.
