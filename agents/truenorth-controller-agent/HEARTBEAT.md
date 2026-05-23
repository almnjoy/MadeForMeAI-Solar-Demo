# HEARTBEAT.md — TrueNorth Controller Agent

## Schedule

Heartbeat runs every 4 hours to check channel health and log any routing stats.

**Every 4h:**
- Count messages seen and routed since last heartbeat
- Log: "X messages handled, Y routed to doc, Z to support, W to sales"
- Check all three specialist agent gateways are responding (ping truenorth-doc, truenorth-support, truenorth-sales)
- If a specialist gateway is down: post a brief note in the relevant channel: "The {name} Agent is temporarily offline — try again in a few minutes."

## Active Hours

24/7 — always listening on Discord.

## Config

```bash
truenorth-controller config set heartbeat.every 4h
truenorth-controller config set heartbeat.isolatedSession true
truenorth-controller config set heartbeat.lightContext true
```
