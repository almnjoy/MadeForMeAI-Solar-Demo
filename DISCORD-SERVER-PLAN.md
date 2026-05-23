# TrueNorth Solar — Discord Server Setup Plan

**Server:** discord.gg/GTVDsPya
**Purpose:** AI-powered support, sales, and community hub for TrueNorth Solar customers and prospects.
**Bot tokens:** See OBSIDIAN VAULT/folder/ACCOUNTS.md

---

## Server Structure

### Categories & Channels

```
📢 ANNOUNCEMENTS
  #announcements          (read-only — news, updates, promotions)
  #welcome                (read-only — pinned onboarding message)

🤖 AI AGENTS
  #solar-knowledge        ← Doc Agent (truenorth-doc)
  #solar-support          ← Support Agent (truenorth-support)
  #solar-sales            ← Sales Agent (truenorth-sales)

🔔 TEAM (internal — hidden from public)
  #new-leads              ← Lead Agent posts here (truenorth-lead)
  #team-chat              ← Internal team channel (text)
  #alerts                 ← System alerts, agent errors, K8s events

💬 COMMUNITY
  #general                (open chat — homeowners, customers)
  #solar-savings-wins     (customers share their savings results)
  #province-talk          (discuss solar in AB, BC, MB, SK)

📚 RESOURCES
  #faq                    (read-only — pinned FAQ answers)
  #links                  (read-only — docs, quote form, support email)
```

---

## Roles

| Role | Color | Who Gets It | Permissions |
|---|---|---|---|
| `@TrueNorth Team` | Gold (#F59E0B) | Tamara, Carlos, Dustin | See #team-chat, #new-leads, #alerts |
| `@Admin` | Red | Dustin only | Full server admin |
| `@Customer` | Green (#10B981) | Verified customers | All public channels |
| `@Solar Curious` | Blue | Anyone who joins | Public channels only |
| `@Doc Agent` | Blurple | Bot | Read/Write #solar-knowledge |
| `@Support Agent` | Blurple | Bot | Read/Write #solar-support |
| `@Sales Agent` | Blurple | Bot | Read/Write #solar-sales |
| `@Lead Agent` | Blurple | Bot | Write-only #new-leads |

---

## Channel Permissions

### AI Agent channels (#solar-knowledge, #solar-support, #solar-sales)
- Everyone can read and write (send messages)
- Bot has `Send Messages`, `Read Message History`, `Embed Links`
- Disable `@everyone` mentioning in these channels

### Team channels (#new-leads, #team-chat, #alerts)
- Only `@TrueNorth Team` and `@Admin` can see these
- Remove `@everyone` view permissions entirely
- Lead bot can only write to #new-leads (no read needed)

### Announcements / Resources (read-only)
- `@everyone` can read
- Only `@Admin` can write
- Bots cannot post here

---

## Welcome Message (pin in #welcome)

> **Welcome to TrueNorth Solar!** ☀️
>
> We're glad you're here. Whether you're exploring solar for the first time or an existing customer with questions, our AI team is available 24/7.
>
> **Where to go:**
> 📚 **#solar-knowledge** — How solar works, specs, installation questions
> 🛠️ **#solar-support** — Issues with your existing system, warranty, service
> 💼 **#solar-sales** — Quotes, pricing, and talking to our team
>
> Ready to get started? → **[Get a Free Quote](https://truenorth.madeformeai.com/get-quote)**
>
> Questions? Email us at **support@truenorthsolar.ca**

---

## Bot Setup (per agent)

Each agent needs its own Discord application and bot. Tokens are in ACCOUNTS.md.

### Step 1 — Invite bots to server

Use the OAuth URLs from ACCOUNTS.md with `bot` + `applications.commands` scopes:
- Doc Agent: `https://discord.com/oauth2/authorize?client_id=1507196258270449664&scope=bot&permissions=68608`
- Support Agent: `https://discord.com/oauth2/authorize?client_id=1507195510568652800&scope=bot&permissions=68608`
- Sales Agent: `https://discord.com/oauth2/authorize?client_id=1507196498172055672&scope=bot&permissions=68608`
- Lead Agent: `https://discord.com/oauth2/authorize?client_id=1507529319671533698&scope=bot&permissions=2048`
- Controller: `https://discord.com/oauth2/authorize?client_id=1507529630062739457&scope=bot&permissions=68608`

Permissions integer `68608` = Send Messages + Read Message History + Embed Links + Add Reactions
Permissions integer `2048` = Send Messages only (Lead Agent — write to #new-leads)

### Step 2 — Assign roles to bots after invite

Go to Server Settings → Members → find each bot → assign the matching `@Doc Agent` / `@Support Agent` etc. role.

### Step 3 — Wire bot tokens to Hermes profiles

Inside the truenorth-hermes pod:
```bash
# Edit each profile .env
nano ~/.hermes/profiles/truenorth-doc/.env
# DISCORD_BOT_TOKEN=MTUwNzE5NjI1ODI3MDQ0OTY2NA...
# DISCORD_CHANNEL_ID=<#solar-knowledge channel ID>

nano ~/.hermes/profiles/truenorth-support/.env
# DISCORD_BOT_TOKEN=MTUwNzE5NTUxMDU2ODY1MjgwMA...
# DISCORD_CHANNEL_ID=<#solar-support channel ID>

nano ~/.hermes/profiles/truenorth-sales/.env
# DISCORD_BOT_TOKEN=MTUwNzE5NjQ5ODE3MjA1NTY3Mg...
# DISCORD_CHANNEL_ID=<#solar-sales channel ID>

nano ~/.hermes/profiles/truenorth-lead/.env
# DISCORD_BOT_TOKEN=MTUwNzUyOTMxOTY3MTUzMzY5OA...
# DISCORD_CHANNEL_ID=<#new-leads channel ID>

nano ~/.hermes/profiles/truenorth-controller/.env
# DISCORD_BOT_TOKEN=MTUwNzUyOTYzMDA2MjczOTQ1Nw...
```

### Step 4 — Restrict bots to their channels

In each AI Agent channel → Edit Channel → Permissions:
- Add the matching bot role
- Allow: View Channel, Send Messages, Read Message History, Embed Links
- Remove all bot roles from channels they shouldn't be in

### Step 5 — Start gateways

```bash
kubectl exec -it -n truenorth-demo deploy/truenorth-hermes -- /bin/sh
truenorth-doc gateway install && truenorth-doc gateway start
truenorth-support gateway install && truenorth-support gateway start
truenorth-sales gateway install && truenorth-sales gateway start
truenorth-lead gateway install && truenorth-lead gateway start
```

---

## Server Events (optional, for demo polish)

| Event | Schedule | Description |
|---|---|---|
| Solar Q&A Live | Weekly, Thursdays 7pm MT | Live session with the team + AI agents |
| New Savings Spotlight | Monthly | Feature a customer win in #solar-savings-wins |

---

## Getting Channel IDs

After creating channels, right-click the channel → Copy Channel ID (need Developer Mode on: User Settings → Advanced → Developer Mode).

You'll need these IDs for the Hermes .env files above.

---

## Checklist

- [ ] Server created at discord.gg/GTVDsPya (verify invite is active)
- [ ] Create all categories and channels from structure above
- [ ] Set channel permissions per role
- [ ] Create bot roles in Server Settings
- [ ] Invite all 5 bots via OAuth URLs
- [ ] Assign bot roles to each bot
- [ ] Pin welcome message in #welcome
- [ ] Pin FAQ in #faq
- [ ] Pin links in #links
- [ ] Copy channel IDs for #solar-knowledge, #solar-support, #solar-sales, #new-leads
- [ ] Update Hermes .env files with bot tokens + channel IDs
- [ ] Start all 4 active bot gateways
- [ ] Test: ask a question in #solar-knowledge — Doc Agent should respond
- [ ] Test: ask a sales question in #solar-sales — Sales Agent should respond + create CRM lead
- [ ] Test: submit a quote form on website — verify Lead Agent posts to #new-leads
