# TrueNorth Solar Demo — Gap Analysis
**What's built. What's missing. What to do next.**
_Last updated: May 2026_

---

## State of the Platform

The core demo is live. The public marketing site, team dashboard, Authentik SSO, Twenty CRM, Hermes, OpenClaw, and Mintlify docs are all running. This is a fully functional product, not a mockup.

---

## ✅ What's Done

| Area | Status |
|---|---|
| React SPA — public marketing site | Live at truenorth.madeformeai.com |
| React SPA — team dashboard (pipeline, team, resources, account) | Live, Authentik-gated |
| Authentik SSO (PKCE OAuth2, proxy outpost, forward auth) | Live |
| Twenty CRM — deployed, workspace configured | Live |
| Get-a-Quote form → Twenty CRM lead creation | Working (createPerson + createOpportunity) |
| Hermes AI runtime — 3 active profiles (doc, support, sales) | Live |
| OpenClaw gateway — wired to Hermes | Live |
| Mintlify docs — 9 pages live | docs-truenorth.madeformeai.com |
| Agent runbooks (truenorth-runbooks/) — all 6 files | Complete |
| Agent files for doc, support, sales (IDENTITY/SOUL/AGENTS/HEARTBEAT) | Complete |
| Agent files for lead, controller | Written — not deployed |
| OpenClaw config (openclaw-config.json) | Updated with 3 solar agents |
| TrueNorth logo SVG (light + dark) | In Branding/ |
| Customer Runbook PDF (v2.0) | In Branding/ |
| Discord server invite | discord.gg/GTVDsPya (server needs full setup) |
| Obsidian vault — updated + accurate | Done |
| Enterprise README.md | In repo |

---

## 🔴 Critical Gaps (blocks the demo)

### 1. Discord bots not wired to Hermes

**What's missing:** The 5 Discord bots exist (tokens in ACCOUNTS.md) but their tokens haven't been added to the Hermes profiles. The bots are not running.

**Impact:** The main demo moment — "talk to the AI agent right here in Discord" — doesn't work.

**Fix:** Wire bot tokens to Hermes `.env` files, start gateways.
See: `DISCORD-SERVER-PLAN.md` → Steps 3-5.
Time: ~30 minutes.

---

### 2. Discord server channels not set up

**What's missing:** The server at discord.gg/GTVDsPya exists but likely has default channels, not the planned structure.

**Impact:** No #solar-knowledge / #solar-support / #solar-sales / #new-leads channels for bots to operate in.

**Fix:** Create categories and channels per `DISCORD-SERVER-PLAN.md`, set permissions, invite bots.
Time: ~45 minutes.

---

### 3. Twenty CRM has no seed data

**What's missing:** The Twenty CRM workspace is configured but empty. No fake leads in the pipeline.

**Impact:** The pipeline Kanban board in the dashboard shows nothing. Zero visual impact in a demo.

**Fix:** Seed 8-10 realistic leads across pipeline stages directly in Twenty CRM (twenty.madeformeai.com).

Suggested seed data:
| Name | Province | Bill | Stage |
|---|---|---|---|
| Sarah Mitchell | Alberta | $220/mo | WON |
| James Thornton | British Columbia | $185/mo | PROPOSAL_SENT |
| Maria Kowalski | Manitoba | $260/mo | SITE_SURVEY |
| Derek Patel | Alberta | $195/mo | CONTACTED |
| Alicia Fontaine | Saskatchewan | $240/mo | CONTACTED |
| Tom Brennan | British Columbia | $310/mo | NEW |
| Yuki Nakamura | Alberta | $175/mo | NEW |
| Claire Bouchard | Manitoba | $290/mo | LOST |
| Ryan McAllister | British Columbia | $210/mo | WON |
| Priya Sharma | Alberta | $235/mo | PROPOSAL_SENT |

Time: ~20 minutes of manual entry in Twenty CRM (or script it via API).

---

## 🟡 Important Gaps (weakens the demo, not blockers)

### 4. Team page shows placeholder rep names

**What's missing:** TeamPage.jsx has "Rep One" and "Rep Two" instead of Tamara and Carlos.

**Fix:**
```jsx
// web2/apps/web/src/pages/TeamPage.jsx
{ name: 'Tamara', role: 'Sales Representative', ... }
{ name: 'Carlos', role: 'Sales Representative', ... }
```
Time: 5 minutes. Needs Docker rebuild after.

---

### 5. Dashboard home page is not wired to live CRM data

**What's missing:** The dashboard homepage stats (leads this week, proposals out, closed deals) are mock data. They don't pull from Twenty CRM.

**Impact:** The "live" dashboard feeling is partly fake. Still looks good, but fragile under scrutiny.

**Fix:** Wire DashboardPage.jsx to Twenty CRM GraphQL API — query opportunity counts grouped by stage, filtered by createdAt this week. The `twentyClient.js` helper is already in place.
Time: ~2 hours.

---

### 6. truenorth-lead agent not deployed

**What's missing:** The lead notification agent (new CRM lead → Discord #new-leads alert) is written but not deployed as a Hermes profile.

**Impact:** When someone fills out the quote form or the Sales Agent creates a lead, the team gets no immediate notification.

**Fix:** Copy `agents/truenorth-lead-agent/` files into Hermes pod, create profile, wire Lead bot token, start gateway.
Time: ~1 hour.

---

### 7. truenorth-controller agent not deployed

**What's missing:** The message router/orchestrator is written but not deployed.

**Impact:** Off-topic messages in wrong channels aren't handled gracefully. Minor for demo, important for production.

**Fix:** Same as #6 — copy files, create profile, wire Controller bot token.
Time: ~30 minutes.

---

### 8. Mintlify docs not pushed to GitHub

**What's missing:** The mintlify-docs/ folder exists locally with 9 pages of quality content. The GitHub repo (almnjoy/MadeForMeAI-Solar-DemoDocs) needs the files pushed so Mintlify can deploy.

**Context:** There was a broken `.git` during previous session. User needs to run:
```powershell
cd Z:\CustomApps\BUSINESS\PRODUCTION-1\MARKETING\SOLAR-DEMO\mintlify-docs
Remove-Item -Recurse -Force .git  # if .git exists and is broken
git init
git remote add origin https://github.com/almnjoy/MadeForMeAI-Solar-DemoDocs.git
git add .
git commit -m "Initial TrueNorth Solar docs"
git push origin main --force
```
Then connect the repo in app.mintlify.com/quickitprojectsllc-c6f5b722.
Time: 15 minutes.

---

### 9. No Twenty CRM MCP connector

**What's missing:** A lightweight MCP server wrapping the Twenty REST API so Hermes agents can read/write CRM records directly during conversations.

**Impact:** The Sales Agent can't look up a lead while chatting, can't update status, can't add notes. The lead creation from Discord relies on the agent calling Twenty's REST API directly — less clean than an MCP tool.

**Fix:** Build a ~50-line FastMCP server:
```python
# tools: get_leads, create_lead, update_lead_status, add_lead_note, get_lead_detail
# auth: Twenty API key from env
# deploy: sidecar in truenorth-hermes pod or standalone microservice
```
Time: ~3 hours (build + deploy + test).

---

## 🟢 Nice-to-Have (polish, post-demo)

### 10. WhatsApp integration
Lead alerts → WhatsApp group alongside Discord #new-leads.
Requires: Twilio or WhatsApp Business API, phone number, webhook.
Complexity: Medium. Need to pick provider.

### 11. Team page from live Twenty CRM API
Replace mock team data with real Twenty CRM user records.
Makes the Team page actually dynamic — shows real leads assigned per rep.

### 12. Agent Brain page — live agent status
The `/agent-brain` page in the dashboard shows Hermes and OpenClaw links but doesn't pull live status.
Could wire: Hermes health endpoint + OpenClaw status → real green/red indicators per agent profile.

### 13. Horizons public marketing site
The current public site IS the React SPA. The original plan was a separate Hostinger Horizons-generated site.
Verdict: The React SPA is better quality. Skip Horizons unless you want a pure homeowner-facing site separate from the dashboard app.

### 14. HTTPS cert warning on Hermes/OpenClaw subdomains
Task #32 still open. The `*.madeformeai.com` wildcard cert may not be covering truenorth subdomains properly.
Check: `kubectl get cert -n truenorth-demo` and verify wildcard covers all used subdomains.

---

## Priority Order for Next Session

| Priority | Task | Time |
|---|---|---|
| 1 | Discord channels + bots wired | ~1.5 hrs |
| 2 | Seed Twenty CRM with 10 fake leads | ~20 min |
| 3 | Push Mintlify docs to GitHub | ~15 min |
| 4 | Fix Team page names (Tamara/Carlos) | ~5 min + rebuild |
| 5 | Deploy truenorth-lead agent | ~1 hr |
| 6 | Wire dashboard stats to CRM API | ~2 hrs |
| 7 | Build Twenty CRM MCP connector | ~3 hrs |
| 8 | Deploy truenorth-controller agent | ~30 min |

**Minimum viable demo (items 1-3):** ~2 hours. Gives you live agents in Discord and a populated pipeline.

---

## What's Not Needed for the Demo

- WhatsApp (nice, not required)
- Horizons public site (the React SPA is better)
- Kubernetes deploy.sh convenience script
- Advanced reporting in Twenty CRM
- Email automation flows

These can all come in a Phase 3 if this becomes a paid product.

---

## Files Involved

| File | Location |
|---|---|
| Discord setup plan | SOLAR-DEMO/DISCORD-SERVER-PLAN.md |
| Agent configs (lead + controller) | SOLAR-DEMO/agents/truenorth-lead-agent/ |
| Agent configs (lead + controller) | SOLAR-DEMO/agents/truenorth-controller-agent/ |
| OpenClaw config | k8s/openclaw-config.json |
| Customer Runbook PDF | SOLAR-DEMO/Branding/TrueNorth-Customer-Runbook.pdf |
| Mintlify docs | SOLAR-DEMO/mintlify-docs/ |
| TeamPage.jsx | web2/apps/web/src/pages/TeamPage.jsx |
| DashboardPage.jsx | web2/apps/web/src/pages/DashboardPage.jsx |
| twentyClient.js | web2/apps/web/src/lib/twentyClient.js |
