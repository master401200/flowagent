---
name: flowagent
description: |
  Generate images and videos with Google Flow (Nano Banana Pro / 2.1 / 2 Lite for images;
  Veo 3.1 Lite / Fast / Quality and Omni Flash for video) through the user's own signed-in
  Flow tab, using the FlowAGENT CLI or its MCP tools. Covers text-to-image, text-to-video,
  start/end-frame image-to-video, reference "ingredients", extending clips, upscaled
  downloads (2K/4K images, 1080p/4K video) and batch runs from a prompts file.
  Use when the user asks to "generate/make/create an image or video", "use Flow / Veo /
  Nano Banana", "animate this image", "make it longer", "upscale", or "run these prompts".
  NOT for other image/video services or paid APIs.
---

## First-time setup (if the `flowagent` command is missing)

FlowAGENT has two parts: a free Chrome extension and a small local app (CLI + MCP server).

1. Ask the user to add the extension: https://chromewebstore.google.com/detail/flowagent/dienlhpcdmbflfnljhpnhdackigmgacd
   and to open https://flow.google.com signed in (keep the tab visible).
2. Check Node.js 20+ (`node -v`); install it if missing.
3. Install the app: `npm install -g --prefer-online https://flowagent.master401200.workers.dev/flowagent.tgz`
   (on Windows use `npm.cmd`).
4. Run `flowagent status` until it says the extension is connected.

Free plan: 40 images + 30 videos a day, one job at a time, AI agents included. Pro (₹99 a month or
₹599 a year, paid once) removes the daily cap and runs 4 jobs at once. Generation uses the
user's own Google Flow plan and credits; FlowAGENT is not affiliated with Google.

# FlowAGENT

FlowAGENT drives Google Flow in the user's own Chrome tab (signed in to their Google
account), so generations use their Flow plan — no API key. Files are saved to disk.

Prefer the MCP tools (`generate_image`, `generate_video`, `generate_batch`,
`flowagent_status`, `flowagent_doctor`) when they are available; otherwise use the CLI.

## Before generating

Run `flowagent status`. It must show `extension: connected` and a visible Flow tab.

- `not connected` → ask the user to open https://flow.google.com in Chrome with the
  FlowAGENT extension enabled, signed in, and keep that tab visible (not minimised).
- `OUTDATED` → ask them to reload the extension (chrome://extensions → ↻) and the tab.
- Something odd → `flowagent doctor` runs a full self-test without spending credits.

Don't narrate internals (bridge, polling). Tell the user what you're making and where the
files landed.

## Images

```
flowagent image "<prompt>" [--model pro|2|lite] [--aspect 16:9|4:3|1:1|3:4|9:16] [--count 1-4] [--size 1k|2k|4k] [--out <dir>] [--name <prefix>] [--ref <img> ...]
```

Images are generally free on Flow plans. `pro` is the best quality, `lite` the fastest.
`--ref` (up to 10) = image-to-image: edit, restyle or combine the user's images — say what
to keep ("the same bracelet") and what to change.

## Videos

```
flowagent video "<prompt>" [--model lite|fast|quality|omni] [--aspect 16:9|9:16] [--count 1-4]
          [--first <img> [--last <img>]]      # image-to-video (a start frame is required for an end frame)
          [--ingredient <img> ...]            # up to 3 reference images (not with frames)
          [--extend "<what happens next>" ...] # make it longer (count must be 1)
          [--res 360p|720p --duration 4|6|8|10] # Omni Flash only
          [--size 720p|1080p|4k|gif]
          [--voice <name>]                    # a Flow voice by exact name (needs ingredients, not frames)
```

`--project <link|id>` (images and videos) runs the job in that Flow project; the Flow tab
opens it first.

Videos spend the user's Flow credits — roughly Lite 10, Fast 20, Quality 100 per clip.
Default to `lite` unless the user wants higher quality; say what it will cost when you
pick `quality`. A clip takes 1–3 minutes; each extension adds about a minute.

Write video prompts like a shot description: subject, action, camera, light, sound.
Keep one continuous shot per clip.

**Talking characters (free voices).** Veo 3.1 speaks dialogue written in the prompt, in any
language. Left to itself it reuses a few stock voices, so different characters sound alike and
one character drifts between clips. Best fix — a **saved Flow voice per character**:

```
flowagent voice "<name>" --base <Flow voice> --style "<how it sounds>"   # free; skipped if it exists
flowagent voices cast.json                                               # many: [{ name, base, style }]
flowagent video "<shot>. <name> says in Hindi: \"…\" He says only these words." --model fast \
          --ingredient <character sheet> --ingredient <location plate> --voice <name>
```

Voices are per Google account: run `flowagent voices cast.json` before each batch (it only creates
missing ones). A voice needs Ingredients mode (no `--first`/`--last`); up to 3 images + the voice
fit. Base voices include Algenib (gravelly, low), Charon/Enceladus/Umbriel (lower), Orus/Alnilam/
Iapetus (firm or clear, mid-low), Achird (friendly), Fenrir (younger), Sadachbia (lively, low),
Gacrux (female, mature), Leda (female, youthful), Achernar (female, soft, high). Without a saved
voice, pin the voice down in words and end the line (`… says only these words: "…", then stays silent`).

**Music.** Veo often adds a music bed even when the prompt says "no music" (sometimes with
sung lyrics). Tell the user to mute or replace clip audio in their editor when that matters.

**Several Google accounts.** With Pro, `flowagent batch … --all-windows` runs jobs in every
visible Flow window at once (one Chrome profile per account); a window whose account is out
of credits hands its videos to the others.

## Merging clips

`flowagent merge a.mp4 b.mp4 c.mp4 --out reel.mp4 [--fade 0.5] [--size 1080x1920]` joins
clips locally with ffmpeg (no credits). For a multi-shot video: generate the shots (a batch),
check each one, then merge in story order. Never merge around a missing/refused shot —
report the gap and regenerate it first.

## Batches

```
flowagent batch prompts.txt [--kind image|video] [--parallel 4] [--out <dir>] [defaults…]
```

`.txt` = one prompt per line. `.csv` columns: prompt, kind, model, aspect, count, res,
duration, first, last, ingredients (a|b), extend (p1|p2), size, name. `.json` / `.jsonl`
take the same keys. Results go to `<out>/results.json`. Pro runs 4 jobs at once; Free runs
one at a time. Add `--merge` to join a video batch into `merged.mp4`.

Name long runs: `--run <name>`. If anything interrupts it (terminal closed, free limit,
restart), `flowagent resume <name>` finishes only what's missing — never re-run the whole
batch. `flowagent runs` lists runs. Refused prompts are skipped on resume; rewrite them in
the batch file first.

FlowAGENT pauses new jobs automatically after Flow refuses a prompt; don't fight it with
retries. `--pace calm` is gentler for very long runs.

## Errors

Every error has a code and a fix. Follow the fix; don't retry blindly.

| code | what to do |
|---|---|
| POLICY | Flow refused the prompt. Rephrase it; never resend the same prompt. |
| OUT_OF_CREDITS | Tell the user their Flow credits are used up. Stop. |
| RATE_LIMIT | Wait a few minutes or use fewer parallel jobs. |
| CAPTCHA / SIGNED_OUT | The user must act in the Flow tab. Never try to solve a captcha. |
| FREE_LIMIT / PRO_REQUIRED | Free plan limit; tell the user, mention Pro. Don't work around it. |
| UI_CHANGED | Run `flowagent doctor` and report which check failed. |
| INTERRUPTED | The Flow tab was reloaded; `flowagent resume` the run or retry the job. |
| EXTENSION_NOT_CONNECTED | See "Before generating". |

If something keeps failing, run `flowagent report` and give the user the file it prints —
it is redacted and ready to send to FlowAGENT support.
