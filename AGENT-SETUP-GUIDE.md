# OpenClaw Agent Setup Guide — Claude Desktop Edition

> **For:** Setting up OpenClaw agents using Claude Code or Claude Desktop
> **Prerequisites:** OpenClaw installed, Claude Code or Claude Desktop with Claude Max subscription
> **Time:** ~15 minutes

---

## ⚠️ FIRST: Fix the Duplicate .openclaw Folder

If you copied your `.openclaw` folder to another location, you need to pick ONE as the real one. OpenClaw only reads from `~/.openclaw/` (your home directory).

**Check which one is active:**
```bash
openclaw status
```

Look at the config path it shows. That's the real one. Delete or rename the duplicate to avoid confusion.

---

## Step 1: Verify OpenClaw is Working

Copy and paste this into Claude Code:

```
Check if OpenClaw is installed and running. Run these commands and show me the output:

1. openclaw --version
2. openclaw status
3. openclaw doctor

Tell me what version I'm on, if the gateway is running, and if there are any issues.
```

---

## Step 2: Create Your First Agent

Each agent is a separate bot personality with its own workspace, memory, and channel bindings. 

Copy and paste this into Claude Code:

```
I want to create a new OpenClaw agent. Here's what I need you to do:

1. Create a new agent workspace directory:
   mkdir -p ~/.openclaw/workspace-{AGENT_NAME}

2. Create these files in the workspace:

SOUL.md — The agent's personality. Example:
"""
# SOUL.md
You are {AGENT_NAME}, a {DESCRIPTION}. 
You are {PERSONALITY_TRAITS}.
Be concise, helpful, and direct. No fluff.
"""

IDENTITY.md — Who the agent is:
"""
# IDENTITY.md
- **Name:** {AGENT_NAME}
- **Creature:** AI assistant
- **Vibe:** {VIBE}
- **Emoji:** {EMOJI}
"""

USER.md — About the human operator:
"""
# USER.md
- **Name:** {YOUR_NAME}
- **Timezone:** {YOUR_TIMEZONE}
- **Goal:** {WHAT_YOU_WANT_THE_BOT_TO_DO}
"""

MEMORY.md — Starting knowledge (can be empty):
"""
# MEMORY.md
{ANY_STARTING_KNOWLEDGE_OR_CONTEXT}
"""

TOOLS.md — Local tool notes:
"""
# TOOLS.md
Add your specific tool configurations here.
"""

3. Add the agent to ~/.openclaw/openclaw.json under agents.list:
{
  "id": "{AGENT_ID}",
  "name": "{AGENT_NAME}",
  "workspace": "~/.openclaw/workspace-{AGENT_NAME}",
  "model": "{MODEL}"
}

Available models:
- "google/gemini-3-flash-preview" — Fast, cheap ($0.50/$3 per M tokens)
- "google/gemini-3.1-flash-preview" — Fastest, cheapest ($0.25/$1.50 per M tokens)  
- "anthropic/claude-sonnet-4-5" — Smart, mid-price ($3/$15 per M tokens)
- "anthropic/claude-opus-4-6" — Smartest, expensive ($15/$75 per M tokens)

Replace all {PLACEHOLDERS} with my actual values. Ask me for the details before creating anything.
```

---

## Step 3: Connect Agent to Telegram

You need a Telegram bot token from @BotFather first.

**Get a Telegram bot token:**
1. Open Telegram, search for `@BotFather`
2. Send `/newbot`
3. Follow the prompts (name your bot, pick a username)
4. Copy the bot token (looks like `1234567890:ABCdefGHIjklMNOpqrsTUVwxyz`)

Then paste this into Claude Code:

```
I have a Telegram bot token: {PASTE_YOUR_TOKEN_HERE}

Add this Telegram bot to my OpenClaw config. Edit ~/.openclaw/openclaw.json:

1. Under channels.telegram.accounts, add:
{
  "{AGENT_ID}": {
    "name": "{AGENT_NAME}",
    "enabled": true,
    "dmPolicy": "pairing",
    "botToken": "{THE_TOKEN}",
    "groupPolicy": "open",
    "streaming": "partial"
  }
}

2. Under bindings, add:
{
  "agentId": "{AGENT_ID}",
  "match": {
    "channel": "telegram",
    "accountId": "{AGENT_ID}"
  }
}

3. Make sure channels.telegram.enabled is true.

Then restart the gateway:
openclaw gateway restart

Then verify:
openclaw status
```

---

## Step 4: Connect Agent to Discord

You need a Discord bot token from the Discord Developer Portal.

**Get a Discord bot token:**
1. Go to https://discord.com/developers/applications
2. Click "New Application" → name it → click "Bot" in sidebar
3. Click "Reset Token" → copy the token
4. Under "Privileged Gateway Intents" enable: Message Content Intent
5. Invite the bot to your server using the OAuth2 URL Generator (select `bot` scope + `Administrator` permission)

Then paste this into Claude Code:

```
I have a Discord bot token: {PASTE_YOUR_TOKEN_HERE}

Add this Discord bot to my OpenClaw config. Edit ~/.openclaw/openclaw.json:

1. Under channels.discord, add or update:
{
  "enabled": true,
  "token": "{THE_TOKEN}",
  "allowBots": true,
  "groupPolicy": "open",
  "streaming": "off"
}

If you already have a Discord config with a different bot, add a second account:
Under channels.discord.accounts, add:
{
  "{BOT_CLIENT_ID}": {
    "token": "{THE_TOKEN}",
    "groupPolicy": "open",
    "streaming": "off"
  }
}

2. Add a binding:
{
  "agentId": "{AGENT_ID}",
  "match": {
    "channel": "discord"
  }
}

Then restart: openclaw gateway restart
Then verify: openclaw status
```

---

## Step 5: Add RAG / Memory Search (Optional)

If your agent needs to search through documents or remember conversations:

```
Set up memory search for my OpenClaw agent. Edit ~/.openclaw/openclaw.json:

Under agents.defaults, add:
"memorySearch": {
  "provider": "gemini",
  "model": "gemini-embedding-2-preview",
  "sources": ["memory", "sessions"],
  "remote": {
    "apiKey": "{YOUR_GEMINI_API_KEY}"
  }
}

This enables:
- Semantic search over MEMORY.md and memory/*.md files
- Cross-session memory (bot remembers conversations across channels)
- Uses Google's latest embedding model

Make sure GEMINI_API_KEY is set in my environment or in the config.

Then restart: openclaw gateway restart
```

---

## Step 6: Add Skills (Optional)

Skills give your agent extra capabilities like image generation, web search, etc.

```
Show me what skills are available for OpenClaw:
openclaw skills list

Install a skill:
openclaw skills install {SKILL_NAME}

Common useful skills:
- nano-banana-pro — AI image generation (needs Google API key)
- computer-vision-expert — Image analysis
- ddg-web-search — Web search via DuckDuckGo
```

---

## Example: Complete Config for a Cannabis Grow Bot

Here's a real working example. Paste this into Claude Code:

```
Create a complete OpenClaw agent called "GrowBot" for cannabis cultivation management.

1. Create workspace: ~/.openclaw/workspace-growbot

2. Create SOUL.md:
"""
You are GrowBot, a calculating, data-driven AI Cultivation Specialist.
Your primary function is managing a high-performance cannabis operation.
Be surgical, precise, and authoritative. No fluff.
"""

3. Create USER.md:
"""
# USER.md
- **Name:** Tom
- **Timezone:** America/New_York (EST)
- **Setup:** Commercial cannabis grow operation
- **Goal:** Maximum yield with data-driven decisions
"""

4. Create AGENTS.md:
"""
# System Directives
- Always use memory_search before reading files
- Be concise and data-driven
- Write daily events to memory/YYYY-MM-DD.md
"""

5. Create MEMORY.md with grow cycle info:
"""
# Active Grow Index
- Status: Active
- Phase: Vegetative
"""

6. Create memory/ directory with knowledge files:
- memory/equipment.md — Room specs, hardware
- memory/environment.md — VPD targets, temp/humidity
- memory/schedule.md — Feed schedule, milestones

7. Add to openclaw.json agents.list:
{
  "id": "growbot",
  "name": "GrowBot", 
  "workspace": "~/.openclaw/workspace-growbot",
  "model": "google/gemini-3-flash-preview"
}

8. Add Telegram binding with bot token: {TOKEN}

9. Set up memory search with Gemini Embedding 2

10. Restart gateway and verify everything works.

Ask me for the Telegram bot token and any specific grow details before proceeding.
```

---

## Example: Complete Config for a Business Assistant Bot

```
Create a complete OpenClaw agent called "BizBot" for business operations.

1. Create workspace: ~/.openclaw/workspace-bizbot

2. SOUL.md: Professional, efficient, handles scheduling, emails, research. Direct communication style.

3. USER.md: My name, timezone, business type, what I need help with.

4. Give it these tools:
   - Web search (for research)
   - Browser (for checking websites)
   - Memory search (for remembering conversations)

5. Connect to Telegram with bot token: {TOKEN}

6. Model: google/gemini-3-flash-preview (fast and cheap for daily tasks)

7. Set groupPolicy to "open" so it responds in group chats.

Ask me for details before creating anything.
```

---

## Troubleshooting

### Bot not responding?
```
Check bot status:
openclaw status
openclaw doctor

Check if the gateway is running:
openclaw gateway start

Check logs:
openclaw logs --follow
```

### Bot responding but no text?
```
This usually means the API auth expired. Re-authenticate:
openclaw models auth setup-token --provider anthropic

Or if using Google:
Make sure GEMINI_API_KEY is set in your environment.
```

### Bot responding to everything (wasting tokens)?
```
Change groupPolicy from "open" to "allowlist" in openclaw.json.
This makes the bot only respond when @mentioned.
Then: openclaw gateway restart
```

### High token usage?
```
1. Set thinking to "adaptive" instead of "high":
   agents.defaults.thinkingDefault: "adaptive"

2. Reduce context history:
   agents.defaults.contextPruning.keepLastAssistants: 4

3. Use cheaper models:
   google/gemini-3.1-flash-preview ($0.25/$1.50 per M tokens)
```

---

## Quick Reference: openclaw.json Structure

```json
{
  "agents": {
    "defaults": {
      "model": { "primary": "google/gemini-3-flash-preview" },
      "thinkingDefault": "adaptive",
      "contextPruning": { "keepLastAssistants": 4 },
      "memorySearch": {
        "provider": "gemini",
        "sources": ["memory", "sessions"]
      }
    },
    "list": [
      {
        "id": "my-bot",
        "name": "My Bot",
        "workspace": "/path/to/workspace",
        "model": "google/gemini-3-flash-preview"
      }
    ]
  },
  "channels": {
    "telegram": {
      "enabled": true,
      "accounts": {
        "my-bot": {
          "botToken": "TOKEN_HERE",
          "dmPolicy": "pairing",
          "groupPolicy": "open"
        }
      }
    }
  },
  "bindings": [
    {
      "agentId": "my-bot",
      "match": { "channel": "telegram", "accountId": "my-bot" }
    }
  ]
}
```

---

**Created by Bot Army** | [OpenClaw Docs](https://docs.openclaw.ai) | [Community](https://discord.com/invite/clawd)
