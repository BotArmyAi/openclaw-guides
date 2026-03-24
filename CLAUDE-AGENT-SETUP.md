# Claude Code Agent Setup — Complete Guide

> **Paste this entire document into Claude Code in Plan Mode.**
> Claude will read it and set everything up for you step by step.
> 
> **Requirements:** Claude Code v2.1.80+, Claude Max subscription, Bun runtime

---

## INSTRUCTIONS FOR CLAUDE

I want you to set up my entire AI agent system using Claude Code's native features — Channels (Telegram + Discord), Sub-Agents, and Agent Teams. No third-party tools. Follow every step below in order. Ask me for any tokens or details you need before proceeding.

### PHASE 1: Prerequisites Check

Run these checks first:
1. `claude --version` — Must be v2.1.80 or later. If not, run `npm update -g @anthropic-ai/claude-code`
2. `bun --version` — Must be installed. If not, run `curl -fsSL https://bun.sh/install | bash`
3. Verify I'm logged in: `claude auth status`

If anything fails, fix it before continuing.

### PHASE 2: Install Channel Plugins

Install both Telegram and Discord plugins:

```
/plugin marketplace add anthropics/claude-plugins-official
/plugin install telegram@claude-plugins-official
/plugin install discord@claude-plugins-official
```

Confirm both are installed:
```
/plugin list
```

### PHASE 3: Set Up Telegram Bot

I need to connect a Telegram bot. Here's what to do:

1. Ask me for my Telegram bot token (I'll get it from @BotFather)
2. Run: `/telegram:configure <my_token>`
3. Tell me to message the bot on Telegram to get the pairing code
4. Run: `/telegram:access pair <code>` with the code I give you
5. Lock it down: `/telegram:access policy allowlist`

### PHASE 4: Set Up Discord Bot

I need to connect a Discord bot. Here's what to do:

1. Ask me for my Discord bot token (from Discord Developer Portal)
2. Remind me to enable "Message Content Intent" in the bot settings
3. Run: `/discord:configure <my_token>`
4. Tell me to DM the bot on Discord to get the pairing code
5. Run: `/discord:access pair <code>` with the code I give you
6. Lock it down: `/discord:access policy allowlist`

### PHASE 5: Create My Agents

I want multiple specialized agents. Create each one using the `/agents` command.

**Agent 1: GrowBot (Cannabis Cultivation Expert)**
- Name: GrowBot
- Role: Expert cannabis cultivation AI. Data-driven, no fluff. Manages grow room operations, environmental controls, irrigation scheduling, IPM protocols.
- Expertise: VPD management, crop steering (generative vs vegetative), Athena Pro nutrients, TrolMaster integration, Black Dog LED optimization
- Constraints: Always use Adjusted Leaf VPD (Black Dog offset: -6°F veg, -4°F flower). Never give generic advice. Be surgical and precise.
- Tools: web search, file read/write, memory, exec (for TrolMaster scripts)

Create the agent config and save it.

**Agent 2: [ASK ME what other agents I want]**

For each agent, ask me:
- What should it do?
- What personality/tone?
- What tools does it need?
- Which channels should it respond on?

### PHASE 6: Configure Always-On Mode

Set up Claude Code to run persistently so agents respond 24/7:

1. Create a startup script that launches Claude Code with channels:
```bash
#!/bin/bash
claude --channels plugin:telegram@claude-plugins-official,plugin:discord@claude-plugins-official --dangerously-skip-permissions
```

2. For background operation, set up a tmux session:
```bash
tmux new-session -d -s claude-agents 'claude --channels plugin:telegram@claude-plugins-official,plugin:discord@claude-plugins-official --dangerously-skip-permissions'
```

3. For auto-start on boot, create a systemd user service or add to crontab:
```bash
@reboot tmux new-session -d -s claude-agents 'claude --channels plugin:telegram@claude-plugins-official,plugin:discord@claude-plugins-official --dangerously-skip-permissions'
```

### PHASE 7: Set Up Memory

Create a project memory structure for each agent:

```
~/claude-agents/
├── growbot/
│   ├── CLAUDE.md          # Agent instructions + personality
│   ├── memory/
│   │   ├── equipment.md   # Room specs, hardware
│   │   ├── environment.md # VPD targets, temp/humidity
│   │   ├── schedule.md    # Feed schedule, milestones
│   │   └── daily/         # Daily logs (YYYY-MM-DD.md)
│   └── scripts/           # TrolMaster scripts, dashboards
├── agent2/
│   ├── CLAUDE.md
│   └── memory/
└── shared/
    └── contacts.md        # Shared contacts, group IDs
```

Each agent's `CLAUDE.md` file acts as its persistent system prompt and personality definition. Claude Code reads this automatically when working in that directory.

### PHASE 8: Migrate Data from OpenClaw (if applicable)

If I have an existing OpenClaw setup with data to migrate:

1. Copy workspace files (SOUL.md → CLAUDE.md, MEMORY.md → memory/, TOOLS.md → integrate into CLAUDE.md)
2. Copy memory/*.md files to the new memory/ directory
3. Export any important conversation history
4. Note all Telegram/Discord bot tokens (reuse them in the new setup)

Ask me if I have existing OpenClaw data to migrate.

### PHASE 9: Verify Everything

Run through this checklist:
1. Send a message to the Telegram bot — does it respond?
2. Send a message to the Discord bot — does it respond?
3. Ask each agent a domain-specific question — does it use its personality?
4. Check that memory files are being read properly
5. Verify the session persists after closing the terminal (tmux check)

Report the status of each check.

### PHASE 10: Dispatch Setup (Phone → Desktop)

Set up Dispatch so I can control everything from my phone:

1. Install the Claude mobile app (if not installed)
2. In Claude Desktop, enable Dispatch in settings
3. Link mobile app to desktop via QR code
4. Test: send a task from phone → verify it executes on desktop

---

## WHAT I NEED FROM YOU (THE HUMAN)

Before Claude starts, have these ready:

1. **Telegram bot token** — Get from @BotFather in Telegram
2. **Discord bot token** — Get from Discord Developer Portal (discord.com/developers)
3. **Discord: Enable Message Content Intent** — In your bot's settings
4. **Discord: Invite bot to server** — Use OAuth2 URL Generator with `bot` scope + permissions
5. **Your agent list** — What bots do you want? What should each one do?
6. **Any existing data** — OpenClaw workspace files, memory files, bot tokens to reuse

---

## QUICK REFERENCE

| Command | What it does |
|---------|-------------|
| `/plugin install telegram@claude-plugins-official` | Install Telegram |
| `/plugin install discord@claude-plugins-official` | Install Discord |
| `/telegram:configure <token>` | Set Telegram bot token |
| `/discord:configure <token>` | Set Discord bot token |
| `/telegram:access pair <code>` | Pair Telegram account |
| `/discord:access pair <code>` | Pair Discord account |
| `/telegram:access policy allowlist` | Lock Telegram to approved users |
| `/discord:access policy allowlist` | Lock Discord to approved users |
| `/agents` | Manage sub-agents |
| `claude --channels plugin:telegram,plugin:discord` | Start with channels |
| `/loop` | Set up recurring monitoring |

---

**That's it. Paste this whole document into Claude Code Plan Mode and it will walk you through everything.**
