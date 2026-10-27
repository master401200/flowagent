# FlowAGENT — your AI agent makes the videos, in your own Google Flow

FlowAGENT lets **Claude Code, Codex, Cursor and Gemini CLI** create images and videos in **your own
signed-in Google Flow tab** (Veo 3.1, Nano Banana). Your agent plans the shots; FlowAGENT types each
prompt into Flow, waits for the result and saves every file to your computer.

- Reels and YouTube videos, scene by scene, from one prompt
- Talking characters with a saved voice that stays the same in every clip
- Batches from a prompt list, start/end frames, reference "ingredients", 2K/4K and 1080p/4K downloads
- No API key, no per-image fees: generation uses your own Flow plan and its credits

This repository holds the **Claude Code plugin** (skill + MCP config) and the **Gemini CLI extension**.
FlowAGENT itself is a free Chrome extension plus a small local app.

## Install

**1. The Chrome extension (free):** [Chrome Web Store](https://chromewebstore.google.com/detail/flowagent/dienlhpcdmbflfnljhpnhdackigmgacd).
Then open [flow.google.com](https://flow.google.com) signed in and keep the tab visible.

**2. The local app** (Node.js 20+):

```bash
npm install -g --prefer-online https://flowagent.master401200.workers.dev/flowagent.tgz
flowagent status
```

**3. Your agent**

- Claude Code, as a plugin:
  ```
  /plugin marketplace add master401200/flowagent
  /plugin install flowagent@flowagent
  ```
  Or run `flowagent setup --agent claude`.
- Gemini CLI: `gemini extensions install https://github.com/master401200/flowagent`
- Codex, Cursor, Windsurf, Claude Desktop: `flowagent setup --agent <name>` (or `--agent all`)

Easiest of all: open the FlowAGENT popup → **Setup** → copy the prompt → paste it into your agent.
It installs and connects everything itself.

## Use

> Make a 30-second launch reel for my perfume brand: 4 cinematic vertical shots, gold and black.

> Create a 6-scene Hindi mythology short about Samudra Manthan: storyboard, narrator voice, thumbnail.

Or, in a terminal: `flowagent image "…"`, `flowagent video "…" --model fast`, `flowagent batch prompts.csv`.

## Plans

| | Free | Pro |
|---|---|---|
| Images / videos | 40 + 30 a day | no daily cap |
| Jobs at once | 1 | 4 |
| AI agents (MCP) | ✓ | ✓ |
| Price | ₹0 | ₹99 a month or ₹599 a year, paid once — nothing renews |

Get Pro: <https://flowagent-pay.master401200.workers.dev/buy>

## Safe by design

FlowAGENT works in your own visible Flow tab at a human pace: no proxies, no stealth, no shared
accounts. Flow's own limits and terms still apply to your account. Flow needs a Google AI plan,
and Google adds a visible watermark to Flow output in some regions; see Google's
[Flow help](https://support.google.com/flow/answer/16353333).

FlowAGENT is an independent tool, **not affiliated with Google**. Google Flow, Veo and Nano Banana are Google products.

Website: <https://flowagent.master401200.workers.dev> · Support: master401200@gmail.com

The files in this repository (plugin, skill, configuration) are MIT-licensed. The FlowAGENT app and
extension are distributed separately under their own terms.
