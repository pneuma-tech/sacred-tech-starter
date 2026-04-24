# POSTMORTEM.md

## What we learned the hard way

### Don't use MCP registration with OpenClaw
Every MCP registration path we tried caused the agent 
to hallucinate tool calls. Use REST + curl via exec/shell 
instead. It works reliably.

### Never use `openclaw gateway --force`
This overwrites your config. Always use:
pkill -f openclaw
openclaw gateway

### Use full paths in nano
Never use ~/ shortcuts in config files. 
Always write the full path.

### Load enough context window
For Qwen 2.5 14B, minimum:
lms load qwen2.5-14b-instruct --context-length 24576 --gpu max

### API keys and multiple accounts
If you have multiple accounts with a provider, 
make absolutely sure you know which key belongs 
to which account before spending hours debugging 
a billing error.

### The identity files are sacred
Protect them:
chmod 444 IDENTITY.md SOUL.md TOOLS.md
This stops gateway restarts from overwriting them.

### The WiFi dropping problem (Pop!_OS)
If your host machine WiFi keeps dropping, killing
your local model connection:
- Disable WiFi power management
- Force 5GHz band
- Restart wpa_supplicant
- Set iwlmvm power_scheme=3
These four fixes together solved it permanently.

### A2A "unknown agent id" error
If A2A messages fail with "unknown agent id: default"
check the RECEIVING agent's config:
openclaw config get agents
The defaultAgentId must match an actual agent ID
in that config — not just be set to "main" in theory.

### Don't use openclaw config set for peers JSON
Use it for simple values. For complex nested JSON
like peers arrays, verify carefully — one quote
error breaks the whole config.

### Always back up before config changes
cp ~/.openclaw/openclaw.json ~/.openclaw/openclaw.json.backup-[date]
Do this every single time before touching the config.
