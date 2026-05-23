# TrueNorth Solar — AI Demo Plan

**Tagline:** "Your solar team. Powered by AI."
**Status:** Phase 2 — Active Development (as of May 2026)

---

## Live URLs

| What | URL |
|------|-----|
| Public Website | https://truenorth.madeformeai.com |
| Team Dashboard | https://truenorth.madeformeai.com/dashboard |
| Documentation | https://docs-truenorth.madeformeai.com |
| Twenty CRM | https://truenorth-crm.madeformeai.com |
| Hermes Dashboard | https://hermes-truenorth.madeformeai.com |
| OpenClaw Gateway | https://truenorth-openclaw.madeformeai.com |
| Authentik Login | https://truenorth-login.madeformeai.com |
| Discord | https://discord.gg/GTVDsPya |

---

## What's Built ✅

- React frontend — public site + protected dashboard
- Authentik SSO (PKCE OAuth2) — login, callback, protected routes, admin gating
- Twenty CRM live integration — pipeline Kanban, lead creation from website form
- Hermes AI gateway — 5 agent personalities (sales, support, doc, lead, controller)
- OpenClaw gateway — chat interface wired to Hermes
- Mintlify docs — https://docs-truenorth.madeformeai.com
- K8s namespace — fully deployed on production-controller-2 (178.104.234.55)
- Resources page in dashboard — Discord, Docs, Email, WhatsApp (coming soon)
- Agent Brain admin page — links to Hermes + OpenClaw UIs

---

## What's In Progress 🔄

- Agent Discord wiring — 5 bots + Hermes personalities created, integration in progress
- Team page — currently mock data, needs real team from Twenty CRM API
- WhatsApp — planned, not yet live

---

## What's Next 📋

See `agents/` planning docs and gap analysis in this folder.

---

## Demo Stack Comparison

| Feature | thesolar.pro | TrueNorth |
|---------|-------------|-----------|
| React frontend | ✅ | ✅ |
| Real AI agents | ❌ | ✅ (5 agents) |
| Live CRM | ❌ | ✅ (Twenty CRM) |
| Authenticated dashboard | Partial | ✅ (Authentik SSO) |
| Agent memory | ❌ | ✅ (Hermes memory) |
| Self-contained K8s pod | ❌ | ✅ (truenorth-demo namespace) |
| Docs site | ❌ | ✅ (Mintlify) |
| Lead creation from web form | ❌ | ✅ (→ Twenty CRM) |

---

## Branding

- Primary: #F59E0B (amber/solar yellow)
- Secondary: #10B981 (green)
- Background: #FFFFFF / #F9FAFB
- Sidebar BG: HSL(220, 13%, 15%) dark navy
- Fonts: Manrope (headings) + DM Sans (body)
- Logo: `Branding/truenorth-logo.svg` + deployed at public `/truenorth-logo.svg`

---

## Folder Structure

```
SOLAR-DEMO/
├── MadeForMeAI-Solar-TrueNorthApp/   ← Main app repo (frontend + k8s manifests)
├── mintlify-docs/                     ← Mintlify docs repo → push to almnjoy/mintlify-docs
├── truenorth-runbooks/                ← Agent knowledge base (company, FAQ, sales, support)
├── agents/                            ← Agent persona files (IDENTITY, SOUL, AGENTS, HEARTBEAT)
│   ├── solar-sales-agent/
│   ├── solar-support-agent/
│   ├── solar-doc-agent/
│   └── workspace-document-engineer/   ← Internal tooling agent
├── Branding/                          ← Logos, CSS, PDFs, press release, brand sheet
├── docs-pages/                        ← ARCHIVED — superseded by mintlify-docs/
└── DEMO-PLAN.md                       ← This file
```
