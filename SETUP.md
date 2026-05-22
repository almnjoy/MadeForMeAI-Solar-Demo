# Solar Demo — Setup Guide

## What This Builds

Three Hermes profiles on the devzone controller, each running as a Discord bot in the same server but different channels:

| Profile name | Bot | Channel |
|---|---|---|
| `solar-doc` | Solar Doc bot | #solar-info |
| `solar-support` | Solar Support bot | #solar-support |
| `solar-sales` | Solar Sales bot | #solar-sales |

---

## Prerequisites

- Hermes installed on the devzone controller
- 3 Discord bots created in the Discord Developer Portal (one per agent), each with:
  - Message Content Intent enabled
  - Bot invited to the demo server
  - Bot token copied
- This repo (npwr-runbooks/) pushed to GitHub or accessible on the server

---

## Step 1 — Push the runbooks repo to GitHub

Create a new private repo (e.g. `almnjoy/npwr-demo-runbooks`) and push the `npwr-runbooks/` directory.

```bash
cd MARKETING/SOLAR-DEMO
git init npwr-runbooks
cd npwr-runbooks
git remote add origin https://github.com/almnjoy/npwr-demo-runbooks.git
git add .
git commit -m "Initial NorthernPWR runbooks"
git push -u origin main
```

---

## Step 2 — Create the three Hermes profiles

SSH into the devzone controller, then:

```bash
# Create each profile
hermes profile create solar-doc --description "Solar education agent for NorthernPWR. Answers questions about how solar works, system components, and the install process."
hermes profile create solar-support --description "Customer support agent for NorthernPWR. Handles troubleshooting, warranties, reimbursement, and escalation."
hermes profile create solar-sales --description "Inbound sales agent for NorthernPWR. Qualifies leads and guides prospects toward a free quote. Zero-pressure."
```

---

## Step 3 — Drop in the agent files

For each profile, copy the SOUL.md, AGENTS.md, IDENTITY.md, and HEARTBEAT.md files into the profile's Hermes home directory:

```bash
# solar-doc
cp agents/solar-doc-agent/SOUL.md ~/.hermes/profiles/solar-doc/SOUL.md
cp agents/solar-doc-agent/AGENTS.md ~/.hermes/profiles/solar-doc/AGENTS.md
cp agents/solar-doc-agent/IDENTITY.md ~/.hermes/profiles/solar-doc/IDENTITY.md
cp agents/solar-doc-agent/HEARTBEAT.md ~/.hermes/profiles/solar-doc/HEARTBEAT.md

# solar-support
cp agents/solar-support-agent/SOUL.md ~/.hermes/profiles/solar-support/SOUL.md
cp agents/solar-support-agent/AGENTS.md ~/.hermes/profiles/solar-support/AGENTS.md
cp agents/solar-support-agent/IDENTITY.md ~/.hermes/profiles/solar-support/IDENTITY.md
cp agents/solar-support-agent/HEARTBEAT.md ~/.hermes/profiles/solar-support/HEARTBEAT.md

# solar-sales
cp agents/solar-sales-agent/SOUL.md ~/.hermes/profiles/solar-sales/SOUL.md
cp agents/solar-sales-agent/AGENTS.md ~/.hermes/profiles/solar-sales/AGENTS.md
cp agents/solar-sales-agent/IDENTITY.md ~/.hermes/profiles/solar-sales/IDENTITY.md
cp agents/solar-sales-agent/HEARTBEAT.md ~/.hermes/profiles/solar-sales/HEARTBEAT.md
```

---

## Step 4 — Set the workspace (runbooks) for each profile

Each agent needs its runbooks accessible. Set the terminal working directory to wherever you clone the runbooks on the server:

```bash
# Clone the runbooks repo on the server
git clone https://github.com/almnjoy/npwr-demo-runbooks.git ~/npwr-runbooks

# Set cwd for each profile so agents find the files
solar-doc config set terminal.cwd /root/npwr-runbooks
solar-support config set terminal.cwd /root/npwr-runbooks
solar-sales config set terminal.cwd /root/npwr-runbooks
```

---

## Step 5 — Configure Discord tokens

Edit each profile's .env and drop in the bot token:

```bash
nano ~/.hermes/profiles/solar-doc/.env
# Add:  DISCORD_BOT_TOKEN=<solar-doc-bot-token>
#       DISCORD_ALLOWED_USERS=   (leave blank to allow all, or restrict to your user ID)

nano ~/.hermes/profiles/solar-support/.env
# Add:  DISCORD_BOT_TOKEN=<solar-support-bot-token>

nano ~/.hermes/profiles/solar-sales/.env
# Add:  DISCORD_BOT_TOKEN=<solar-sales-bot-token>
```

Also configure the model for each (if not already set from a clone):

```bash
solar-doc config set model.default anthropic/claude-sonnet-4
solar-support config set model.default anthropic/claude-sonnet-4
solar-sales config set model.default anthropic/claude-sonnet-4
```

---

## Step 6 — Install gateways as services

```bash
solar-doc gateway install
solar-support gateway install
solar-sales gateway install
```

This creates three systemd services: `hermes-gateway-solar-doc`, `hermes-gateway-solar-support`, `hermes-gateway-solar-sales`.

---

## Step 7 — Start and verify

```bash
solar-doc gateway start
solar-support gateway start
solar-sales gateway start

# Check status
hermes profile list
solar-doc doctor
solar-support doctor
solar-sales doctor
```

Go to Discord and test each bot by messaging in its respective channel.

---

## Step 8 — Docs site (Mintlify)

1. Copy the three `.mdx` files from `docs-pages/northernpwr/` into the `northernpwr/` directory in the `almnjoy/docs` repo.
2. Open `mint.json` in that repo and add the nav group from `docs-pages/mint-nav-addition.json` to the `navigation` array.
3. Commit and push — Mintlify will deploy automatically.

---

## Tear Down

```bash
solar-doc gateway stop
solar-support gateway stop
solar-sales gateway stop

# Full removal if needed
hermes profile delete solar-doc --yes
hermes profile delete solar-support --yes
hermes profile delete solar-sales --yes
```
