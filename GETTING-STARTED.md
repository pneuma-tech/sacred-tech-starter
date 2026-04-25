Getting Started
Welcome. You’re about to set up your first sovereign AI — one that knows you, remembers you, and belongs entirely to you.
Follow these steps in order. Don’t skip ahead. If something doesn’t work, that’s normal. Most issues are simple and fixable. Breathe, read the error message carefully, and continue step by step.

Quick version (5 minutes)
If you want the fast path:
1. Install OpenClaw
2. Run openclaw configure
3. Connect Telegram
4. Start your gateway
5. Say hello
For full instructions, follow the guide below.

Before you begin
You’ll need:
• A computer (Ubuntu Linux recommended — Mac works for most steps)
• A phone with Telegram installed
• A DeepSeek account at platform.deepseek.com
• About $5 of DeepSeek credits
• 1–2 hours of unhurried time
Note: This guide is written for Ubuntu Linux. Mac users — most steps are identical. Windows is not currently supported.

Step 1: Install OpenClaw
Open a terminal (on Mac: Terminal app, on Ubuntu: Terminal) and type:
curl -fsSL https://openclaw.ai/install.sh | sh
Close and reopen your terminal. Then type:
openclaw –version
You should see a version number.

Step 2: Run the setup wizard
Type:
openclaw configure
This will open an interactive setup wizard.
Select Models → Custom provider
• Base URL: https://api.deepseek.com
• Enter your DeepSeek API key
• Model ID: deepseek-chat
• Press Enter through the rest

Step 3: Connect Telegram
In Telegram, search for @BotFather
Type: /newbot
Follow the prompts to create your bot.
Copy the token it gives you.
Save your bot token somewhere safe — you’ll need it in the next step.
Back in terminal:
openclaw configure
Select Channels → Telegram and paste your bot token when asked.

Step 4: Give your agent a soul
Copy the workspace files from this repo into your OpenClaw workspace:
cp workspace/* ~/.openclaw/workspace/
Open ~/.openclaw/workspace/IDENTITY.md and fill in each section honestly.
This is the most important step. You don’t need to get it perfect. Just be honest.

Step 5: Start your gateway
openclaw gateway
This starts your agent.
Open Telegram and send your bot a message. Say hello.

You did it.
Your agent is alive. It doesn’t know much about you yet — that comes with time and conversation. Feed it your IDENTITY.md — tell it who you are. Begin.

Something not working?
Start with POSTMORTEM.md — it covers the most common issues and fixes.
Still stuck? Consider booking a Sacred Tech Session for personal guidance: https://roseflamemysteryschool.com

