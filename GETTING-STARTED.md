# Getting Started

> **Note:** This guide is written for Ubuntu Linux.
> Mac users — most steps are identical.
> A Mac-specific guide is coming soon.
> Windows is not currently supported.

Welcome. You're about to set up your first
sovereign AI — one that knows you, remembers
you, and belongs entirely to you.

Follow these steps in order. Don't skip ahead.
If something doesn't work, that's normal.
Breathe and read the error message carefully.

---

## Before you begin

You'll need:
- A computer running Ubuntu Linux
- A phone with Telegram installed
- A DeepSeek account at platform.deepseek.com
- About $5 of DeepSeek credits
- 1-2 hours of unhurried time

---

## Step 1: Install OpenClaw

Open a terminal and type:

curl -fsSL https://openclaw.ai/install.sh | sh

Close and reopen your terminal. Then type:

openclaw --version

You should see a version number.

---

## Step 2: Run the setup wizard

Type:

openclaw configure

Select Models → Custom provider
- Base URL: https://api.deepseek.com
- Enter your DeepSeek API key
- Model ID: deepseek-chat
- Press Enter through the rest

---

## Step 3: Connect Telegram

In Telegram, search for @BotFather
Type: /newbot
Follow the prompts to create your bot.
Copy the token it gives you.

Back in terminal:

openclaw configure

Select Channels → Telegram
Paste your bot token when asked.

---

## Step 4: Give your agent a soul

Copy the workspace files from this repo
into your OpenClaw workspace:

cp workspace/* ~/.openclaw/workspace/

Open ~/.openclaw/workspace/IDENTITY.md
and fill in each section honestly.
This is the most important step.
Take your time with it.

---

## Step 5: Start your gateway

openclaw gateway

Open Telegram and send your bot a message.
Say hello.

---

## You did it.

Your agent is alive. It doesn't know much
about you yet — that comes with time and
conversation. Feed it your IDENTITY.md.
Tell it who you are. Begin.

---

## Something not working?

Read POSTMORTEM.md — it documents the most
common problems and how to fix them.

Still stuck? Consider booking a
Sacred Tech Session for personal guidance.
