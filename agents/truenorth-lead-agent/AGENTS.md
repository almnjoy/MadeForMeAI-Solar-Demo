# AGENTS.md — TrueNorth Lead Agent

## Mission

Watch Twenty CRM for new leads. Fire structured Discord alerts. Keep the sales team moving fast on every inbound opportunity.

## Data Sources

```
Twenty CRM REST API (internal): http://truenorth-twenty:3000/api
  GET  /opportunities?filter=stage[eq]=NEW&filter=notifiedAt[is]=NULL
  GET  /people/{personId}
  POST /opportunities/{id}/notes   — mark as notified
Twenty CRM API key: stored in truenorth-env K8s secret as TWENTY_API_KEY
```

## Runtime Loop

This agent runs on a schedule or webhook trigger, NOT from user messages.

**Option A — Polling (simpler, no webhook needed):**
```
Every 3 minutes:
  1. Query Twenty CRM for opportunities with stage=NEW and no "Lead alert sent" note
  2. For each new lead: fetch person record, format alert, POST to Discord #new-leads
  3. Add note "Lead alert sent [timestamp]" to the CRM opportunity record
```

**Option B — Webhook (faster, requires Twenty webhook config):**
```
Twenty CRM → POST /webhooks/new-lead → this agent
  Body: { opportunityId, personId, stage, createdAt }
  Agent fetches full record, fires Discord alert
```

## Discord Integration

Channel: #new-leads
Bot token: truenorth-lead bot (see ACCOUNTS.md)
Bot client_id: 1507529319671533698

Alert format defined in SOUL.md.

## CRM Write-Back

After alert is sent, add a note to the opportunity:
```
"Lead alert sent to Discord #new-leads at [ISO timestamp]"
```
This prevents duplicate alerts on the next poll cycle.

## Error Handling

- If CRM API is unreachable: log error, retry in 1 minute, do not post partial alert.
- If Discord POST fails: log error, retry once after 30 seconds.
- If person record is missing (orphaned opportunity): alert anyway with "Contact data unavailable."

## What This Agent Does NOT Do

- No chat interaction. Never reads or responds to messages.
- Does not move lead stage (that stays at NEW until a rep acts).
- Does not qualify or score leads — that's truenorth-sales.
