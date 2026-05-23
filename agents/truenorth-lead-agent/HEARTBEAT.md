# HEARTBEAT.md — TrueNorth Lead Agent

## Schedule

This agent does not run on a conversational heartbeat. It runs on a polling loop.

**Poll interval:** Every 3 minutes (configurable via config set heartbeat.intervalMinutes 3)
**Active hours:** 24/7 — leads can come in any time
**Isolated session:** true — each poll is a fresh, lightweight execution

## On Each Heartbeat

1. Check Twenty CRM for unnotified NEW leads
2. Fire Discord alerts for any found
3. Write CRM notes to mark as notified
4. Sleep until next interval

## Status Check

The Hermes dashboard at hermes-truenorth.madeformeai.com shows last run time and last lead notified.

## If Agent Goes Silent

If no heartbeat for > 10 minutes:
- Check `kubectl logs -n truenorth-demo deploy/truenorth-hermes --tail=50`
- Check `truenorth-lead doctor` inside the Hermes pod
- Likely cause: CRM API auth token expired or pod restart
