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
