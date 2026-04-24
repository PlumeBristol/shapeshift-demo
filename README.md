# Shapeshift demo video

A self-playing HTML "product video" in the style of a ChatGPT launch reel:
dark stage, kinetic typography, animated UI mocks, benefit-first copy.

The whole thing is one file (`demo/index.html`) — no build, no deps. Total
runtime ~50s across 9 scenes that loop.

## Opening it

```bash
open demo/index.html
```

(or drag the file into any Chromium browser — Safari is fine too but the
grain filter behaves better in Chrome.)

## Recording to MP4

Three options, from easiest to best:

### 1. Screen recorder (macOS)

`Cmd+Shift+5` → "Record Selected Portion" over the stage area. The video
auto-loops so you can grab a full pass and trim.

Append `?record=1` to the URL — that hides the Play / Restart / Hide UI
controls in the bottom-right so the frame is clean.

### 2. Playwright headless capture

If you want a deterministic ~50s MP4 at 1080p without any chrome, install
Playwright (`npm i -D @playwright/test`) and run:

```js
// capture.js
const { chromium } = require('playwright');
(async () => {
  const browser = await chromium.launch();
  const ctx = await browser.newContext({
    viewport: { width: 1920, height: 1080 },
    recordVideo: { dir: 'demo/out', size: { width: 1920, height: 1080 } },
  });
  const page = await ctx.newPage();
  await page.goto('file://' + require('path').resolve(__dirname, 'index.html') + '?record=1');
  await page.waitForTimeout(50_500);
  await ctx.close();
  await browser.close();
})();
```

`node demo/capture.js` → `demo/out/*.webm`. Convert with ffmpeg if you need
MP4: `ffmpeg -i demo/out/*.webm -c:v libx264 -crf 18 demo/shapeshift.mp4`.

### 3. Obsidian / ScreenFlow / anything else

Same as #1 — just record a full loop pass and trim to one cycle.

## Scenes

| # | Title                       | Duration | Benefit |
|---|-----------------------------|----------|---------|
| 1 | Opening                     | 4.5s     | Authoring that feels like writing, not admin. |
| 2 | Build + populate in one space | 8.5s     | Notion-easy — no more hopping around the LMS backend; whole course visible and rearranges in place. |
| 3 | Slash menu                  | 5.5s     | Every block is one key away. |
| 4 | AI writer                   | 6.5s     | Claude drafts inline, not in a modal. |
| 5 | Course from a file          | 7s       | Drop a deck / brief / doc — get a structured course. |
| 6 | Quiz creation               | 6.5s     | Placeholders commit as you type — no add buttons, no detours. |
| 7 | AI quiz generation          | 7.5s     | One prompt, a full question set — types, answers, correct marks. |
| 8 | File picker                 | 6s       | Multi-select attachments in two clicks. |
| 9 | Undo                        | 5.5s     | Everything is undoable with ⌘Z. |
| 10| Tabs                        | 5.5s     | Content and settings cleanly separated. |
| 11| Closing                     | 5s       | Tagline + wordmark. |

**Total:** ~68s. Loops indefinitely.

## Editing the copy

All the headlines, subheads, and UI strings live in `<section class="scene">`
blocks in `index.html`. Each word lives in its own `<span class="w">` so the
stagger animation can target them individually — preserve that structure
when changing words.

## Editing the timing

Each scene has `data-duration="6000"` (ms). Adjust per scene; the progress
bar scales automatically from the sum.

The word-stagger delays are hard-coded up to 14 words per scene via
`.scene.active .kt .w:nth-child(N)` rules. Add more if a line needs more
words — or move to a JS-generated stagger for arbitrary counts.

## Brand

The palette matches Strapi's primary (`#7c6cff` / `#4945ff`) over a dark
`#09090f` stage. Swap the `:root` CSS variables to rebrand.
