# The agent builds a game from one prompt

A make.fun bounty entry: a short vertical video about an AI coding agent building a tiny
browser game from **one prompt** while the user watches.

## The video

`agent-builds-a-game.mp4` (26 seconds, 1080x1920, H.264 + AAC) tells the story in four
beats, all rendered in the agent session that built this entry:

1. **Hook (0–3s)** — "ONE PROMPT. ONE GAME."
2. **The prompt (3–9s)** — the exact prompt typed: *"Build me a tiny browser game:
   catch falling stars with a basket, 30 seconds, score counter."*
3. **The agent working (9–17s)** — a terminal log of the agent planning, writing and
   testing the game, with a progress bar.
4. **The finished game running (17–26s)** — real gameplay of Star Catcher, captured
   frame by frame from the game itself as it played, ending on "YOUR TURN — build yours
   on make.fun".

The video ends with make.fun's referral end card from the press pack
(`makefun-card-9x16-transparent.webm`, used as-is, laid over the last 2.5 seconds with
the clip blurred and desaturated underneath, per the press guide), carrying the
entrant's own referral code `va8excfs`. The make.fun brand badge runs from the first
frame until the end card.

## The game

[`star-catcher.html`](star-catcher.html) is the finished game the video shows — a real,
playable browser game built from the one prompt above. Open it in any browser; move the
basket with mouse or touch and catch falling stars for 30 seconds. `?autoplay=1` lets the
basket play itself (the mode the video's gameplay was captured in); `?frame=T` renders
one exact moment of the game (used to capture the video's gameplay frames).

## The post

The video is posted natively on X from the public account `@sadewa0981` with the
bounty line and the entrant's play code in its text. `POST.json` names the post's
address; it holds a placeholder until the post is up.

## How it was made

- Game written in this session (`star-catcher.html`, plain HTML + canvas JS).
- Gameplay captured from the real game running in headless Chromium via CDP
  (30 fps, deterministic seeded play), not staged.
- Video rendered with ffmpeg commands run in this session (see `BUILD.md`).
- `main.ts` copies `POST.json` to `/output/POST.json` — that is all the program does.

## Verify locally

```sh
mkdir -p out && MAKEFUN_BOT_DIR=. MAKEFUN_OUTPUT_DIR=out node main.ts
```

`out/POST.json` is then byte for byte this directory's `POST.json`.

## Bounty

[The agent builds a game from one prompt](https://make.fun/bounty/post-x-the-agent-builds-a-game-from-20261122-1709)
— top 10 entries share $20 by post score (views + 10 × likes, reposts, quotes,
replies). Entries close 2026-11-24T17:09:00Z.
