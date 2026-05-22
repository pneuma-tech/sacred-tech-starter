LESSONS.md

What we learned the hard way

Something broken? Start here.

If you’re stuck, check these first:
• Agent acting strangely → avoid MCP, use REST + curl
• Config reset unexpectedly → never use openclaw gateway –force
• Commands not working → use full file paths, not ~/
• Telegram not responding → check your bot token and that gateway is running
• Errors after config edits → restore from backup
Then scroll for full explanations below.

MCP registration caused unreliable tool behavior in at least one documented case. Use REST + curl as a safer alternative until you’ve tested MCP in your specific setup.

⚠️ Never use openclaw gateway –force
This can overwrite your config. Instead use:
pkill -f openclaw
openclaw gateway

Use full paths in config files
Always use full paths, not ~/. Some tools don’t resolve ~ correctly, which can silently break your setup.

Load enough context window
If your model behaves inconsistently or forgets things quickly, your context window may be too small. For Qwen 2.5 14B, minimum:
lms load qwen2.5-14b-instruct –context-length 24576 –gpu max

API keys and multiple accounts
If you have multiple accounts with a provider, make sure you know which key belongs to which account before spending hours debugging a billing error. This is a very common source of silent failure.

The identity files are sacred
Protect them:
chmod 444 IDENTITY.md SOUL.md TOOLS.md
This prevents gateway restarts from overwriting your agent’s identity.

WiFi dropping (Pop!_OS specific)
If your host machine WiFi keeps dropping:
• Disable WiFi power management
• Force 5GHz band
• Restart wpa_supplicant
• Set iwlmvm power_scheme=3

A2A “unknown agent id” error
If you see “unknown agent id: default” check the receiving agent’s config:
openclaw config get agents
The defaultAgentId must match a real agent ID in that config, not just be set to “main” in theory.

Be careful with config set for complex values
Use openclaw config set for simple values only. For complex JSON like peers arrays, edit carefully — one quote error can break everything.

Always back up before changing config
cp ~/.openclaw/openclaw.json ~/.openclaw/openclaw.json.backup-$(date +%F)
Do this every time before editing config.

May 2026 — Agent builds its own MemPalace skill
• OpenClaw tools config key does not use an entries wrapper — that key is invalid and will break the gateway
• Use skill-creator to give agents new capabilities; they can build and self-correct their own tools autonomously
• MemPalace search expects POST with {"query": "..."} body, not GET with ?q= param
• Shared council memory and personal native memory serve different purposes — both valuable
• Never use openclaw gateway --force — use pkill -f openclaw && openclaw gateway

Problem: Writing Python files to disk reliably
The issue: When pasting multi-line Python code into the terminal via nano or through heredoc commands (cat << 'EOF'), indentation gets corrupted and escape characters get mangled, causing SyntaxErrors and IndentationErrors. Single-line python3 -c commands have the same problem with escaped newlines and quotes.
What didn’t work:
• Pasting into nano — indentation corrupted
• cat << 'EOF' heredoc — newlines lost when sent through chat
• python3 -c with embedded newlines — escape sequences mangled
• Patching files with string replacement — quote escaping issues
What worked reliably:
• Create the file cleanly on Claude.ai as a downloadable attachment
• Delete any cached version from Downloads first: rm ~/Downloads/filename.py
• Download the fresh file from the chat
• Copy into place with a single cp command: cp ~/Downloads/filename.py /path/to/destination.py
• Verify with head -5 or grep before running
Key lesson: The download method bypasses all terminal encoding issues because the file is written correctly on Anthropic’s end before it ever touches your terminal.

Lesson: Never run OpenClaw gateway with sudo

Date learned: May 22, 2026
Context: Fresh OpenClaw install on Mac (no VM), setting up new agent Ganesh

What happened:
During install, launchctl bootstrap failed with an I/O error when trying to register the gateway as a background service. As a quick workaround, sudo openclaw gateway run was used to get the gateway running. This appeared to work — the dashboard loaded, the agent responded in the browser — but Telegram never received messages.

Root cause:
Running the gateway with sudo causes all config files to be written as the root user instead of the logged-in user. This means the regular user can’t read or edit the config, channels can’t authenticate properly, and Telegram polling breaks silently.

The rule:

• ✅ Use sudo only to fix permissions (e.g. sudo chown -R $(whoami) ~/.openclaw)
• ❌ Never use sudo to run the gateway
• ❌ Never use openclaw gateway --force (overwrites config)

Correct fix for launchctl error:

sudo chown -R $(whoami) ~/.openclaw
openclaw gateway run


Run as normal user after fixing ownership. Everything stays owned by you.

How to recover if you already ran with sudo:

# Stop the gateway (Ctrl+C)
sudo chown -R $(whoami) ~/.openclaw
openclaw gateway run


Client-facing note:
If a client hits the launchctl error during onboarding, fix permissions first, then run as normal user. Never bypass with sudo — it creates silent downstream failures that are hard to trace.

Lesson: npm global install permissions on Mac with Homebrew

Date learned: May 22, 2026
Context: Fresh OpenClaw install on Mac (Apple Silicon, Homebrew-managed Node)

What happened:
Running the official OpenClaw installer (curl -fsSL https://openclaw.ai/install.sh | bash) failed twice with:

npm error EACCES: permission denied, mkdir '/opt/homebrew/lib/node_modules/openclaw'
npm error code: 'EACCES'


The installer couldn’t write to Homebrew’s global node_modules folder because it was owned by root, not the logged-in user.

Root cause:
On Macs with Homebrew-managed Node, the global npm prefix is /opt/homebrew. The node_modules folder inside it is often owned by root, which blocks global npm installs for regular users.

The fix:

# Step 1: Find your npm prefix (should say /opt/homebrew on Apple Silicon)
npm config get prefix

# Step 2: Fix ownership of the lib folder
sudo chown -R $(whoami) /opt/homebrew/lib

# Step 3: Now install normally — no sudo needed
npm install -g openclaw@latest


Verify it worked:

openclaw --version
# Should print e.g. OpenClaw 2026.5.20


The rule:

• ✅ Use sudo chown to fix folder ownership first
• ✅ Then run npm install -g as normal user
• ❌ Never run sudo npm install -g — this reinstates the root ownership problem and breaks future installs

Client-facing note:
This will happen on almost every fresh Mac install with Homebrew. It’s a one-time fix per machine. After running the chown command once, all future npm global installs work normally without sudo.

Warning signs this is your problem:

• Error says EACCES or permission denied
• Path in error contains /opt/homebrew/lib/node_modules
• Install fails twice in a row (the installer retries once automatically)

Lesson: Device pairing and dashboard auth in OpenClaw v2026.5.20+

Date learned: May 22, 2026
Context: Fresh OpenClaw install, accessing Control UI dashboard for first time

What’s new in this version:
OpenClaw v2026.5.20 introduced a more secure dashboard auth flow. The old openclaw auth pair command no longer exists. Device pairing now happens through a combination of the CLI and the browser Control UI.

What happened:
After starting the gateway, navigating to http://127.0.0.1:18789 showed “Auth required” — the browser couldn’t connect without a token. Then after getting a token, it showed “Device pairing required” with a device ID that needed approval.

The correct flow — step by step:

Step 1: Start the gateway as normal user

openclaw gateway run


Leave this terminal window open and running.

Step 2: Get the tokenized dashboard URL
Open a second terminal window and run:

openclaw dashboard --no-open


This copies a token-authenticated URL to your clipboard automatically. Do NOT navigate to the plain http://127.0.0.1:18789 — use the tokenized URL instead.

Step 3: Paste the tokenized URL into your browser
Cmd+V into the address bar. This pre-fills the gateway token and gets you past the first auth screen.

Step 4: Approve the device pairing request
The browser will show “Device pairing required” with a command like:

openclaw devices approve <device-id>


Copy the exact command shown on screen. Run it in your second terminal window:

sudo openclaw devices approve <device-id-from-screen>


You’ll see Approved <hash> in green when it works.

Step 5: Click Connect in the browser
After approval, go back to the browser and click Connect. The dashboard should load showing “Assistant — Ready to chat.”

Important notes:

• The device ID shown on screen is unique to each browser session — copy it exactly as shown, don’t type from memory
• This is a one-time approval per browser — once approved, that browser connects automatically on future sessions
• The --no-open flag suppresses auto-launch so you can copy the URL manually; without it the browser opens automatically but may not have the token embedded correctly in all cases

The rule:

• ✅ Always use openclaw dashboard --no-open to get the tokenized URL
• ✅ Always approve device pairing from the exact command shown in the browser
• ❌ Don’t navigate to the plain IP address directly — you’ll get stuck in auth loops
• ❌ Don’t look for openclaw auth pair — it no longer exists in this version

Client-facing note:
This flow is new and unfamiliar even to experienced OpenClaw users. Walk clients through it slowly — it looks scarier than it is. The key insight is: the browser tells you exactly what command to run. Just read the screen and follow it. Three steps: tokenized URL → approve device → click Connect.

What this flow protects against:
The new auth system ensures only approved devices can control the gateway. This matters especially when Cloudflare Tunnel is in use and the dashboard is exposed beyond localhost — unauthorized browsers can reach the URL but can’t connect without explicit device approval.

