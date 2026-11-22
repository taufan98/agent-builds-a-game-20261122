# How the video was made

Everything below was decided and run by the agent in the session that built this
entry. No footage was downloaded; every frame was generated or captured in-session.

## Creative decision

Hook in the first seconds, then the three things the brief asks for, in order: the
prompt, the agent working, the finished game running. "ONE PROMPT. ONE GAME." opens
because the whole idea is the contrast — one sentence in, a playable game out — and a
viewer who has ever wished a game into existence stays to see whether it is real. The
payoff is real gameplay (score climbing 50 → 170), not a mockup, so the result is
believable; the video ends on the game with "YOUR TURN", then make.fun's referral end
card with the entrant's own code.

## The game

`star-catcher.html` — written in-session from the one prompt: "Build me a tiny browser
game: catch falling stars with a basket, 30 seconds, score counter." Canvas, seeded RNG
(deterministic), 30-second timer, score counter, mouse/touch controls, `?autoplay=1`
demo mode, `?frame=T` single-moment render mode.

## Gameplay capture (real game, headless Chromium via CDP)

```sh
node capture-frames.mjs frames-9s 9 3
```

`capture-frames.mjs` loads the game with `?autoplay=1&frame=0`, then steps the game's
own `step(1/30); draw();` 270 times over CDP and screenshots each step — 9 seconds of
real gameplay at 30 fps, game time 3s → 12s (score 50 → 170).

## Segments (ffmpeg 8.1.2)

```sh
# 1. Hook, 3s
ffmpeg -f lavfi -i color=c=0x0d0b1e:s=1080x1920:d=3:r=30 -vf \
  "drawtext=...:text='ONE PROMPT.'...:enable='gte(t,0.2)',drawtext=...:text='ONE GAME.'...:enable='gte(t,1.2)',drawtext=...:text='my AI agent built it while I watched'...:enable='gte(t,2.0)'" \
  -c:v libx264 -pix_fmt yuv420p -r 30 seg/s1-hook.mp4

# 2. The prompt, 6s — chat bubble, lines appearing one by one
ffmpeg -f lavfi -i color=c=0x141033:s=1080x1920:d=6:r=30 -vf \
  "drawtext=...:text='THE PROMPT — what I typed'...,drawbox=...:color=0x241d4d:t=fill...,drawtext=fontfile=DejaVuSansMono-Bold:text='Build me a tiny browser'...:enable='gte(t,0.4)',drawtext=...:text='game\: catch falling stars'...:enable='gte(t,1.3)',drawtext=...:text='with a basket, 30 seconds,'...:enable='gte(t,2.2)',drawtext=...:text='score counter.'...:enable='gte(t,3.1)'" \
  -c:v libx264 -pix_fmt yuv420p -r 30 seg/s2-prompt.mp4

# 3. Agent working, 8s — terminal lines + stepped progress bar
ffmpeg -f lavfi -i color=c=0x0a0918:s=1080x1920:d=8:r=30 -vf \
  "drawtext=...:text='AGENT WORKING'...,drawtext=...:text='> reading prompt...'...:enable='gte(t,0.3)',drawtext=...:text='> plan\: canvas game, 30s timer'...:enable='gte(t,1.3)',drawtext=...:text='> writing star-catcher.html'...:enable='gte(t,2.6)',drawtext=...:text='> adding stars + basket + score'...:enable='gte(t,3.9)',drawtext=...:text='> testing... 17 stars caught'...:enable='gte(t,5.2)',drawtext=...:text='> DONE - game ready in 41s'...:enable='gte(t,6.5)'" \
  -c:v libx264 -pix_fmt yuv420p -r 30 seg/s3-agent.mp4

# 4. Gameplay, 9s — captured frames + CTA in the last 1.5s
ffmpeg -framerate 30 -i game/frames-9s/f%04d.png -vf \
  "drawbox=...:color=black@0.55:t=fill:enable='gte(t,7.5)',drawtext=...:text='YOUR TURN'...:enable='gte(t,7.5)',drawtext=...:text='build yours on make.fun'...:enable='gte(t,7.5)'" \
  -c:v libx264 -pix_fmt yuv420p -r 30 seg/s4-game.mp4

# Concat (26s total)
ffmpeg -f concat -safe 0 -i seg/concat.txt -c copy seg/clip.mp4
```

## Final pass — brand badge, press-pack end card, audio

End card fetched in-session from the press pack manifest
(`https://make.fun/press/manifest.json`):
`https://make.fun/press/referral-card/9x16/makefun-card-9x16-transparent.webm`,
used as-is, laid over the last 2.5 s with the clip blurred (sigma 28) and desaturated
(0.35) fading in over 0.8 s, per `https://make.fun/press/llms.txt`. Referral code
`va8excfs` (from `GET /api/me/referrals`) typed into the field at 62 px DejaVu Sans
Mono Bold, ink `#191419`, after the card rests (1.433 s), caret covered in white.
The brand badge (`makefun-badge-9x16.png`) runs from the first frame until the card.
Audio is two soft sine tones (330 + 495 Hz) mixed at low volume with fades.

```sh
ffmpeg -i seg/clip.mp4 -c:v libvpx-vp9 -i card-9x16.webm -i badge-9x16.png \
  -f lavfi -i "sine=frequency=330:duration=26" -f lavfi -i "sine=frequency=495:duration=26" \
  -filter_complex_script final-filter.txt -map '[v]' -map '[a]' \
  -c:v libx264 -pix_fmt yuv420p -crf 18 -c:a aac -b:a 128k -movflags +faststart \
  agent-builds-a-game.mp4
```

## Verification

- `ffprobe`: 26.000 s, 1080x1920, H.264 + AAC, 1.57 MB.
- Frames inspected at 1s (hook), 5s (prompt), 12s (agent), 20s (gameplay),
  25s and last frame (end card, code `va8excfs` legible in the field). Two layout
  defects found and fixed in-session: the badge covered "AGENT WORKING" and the
  score, and a drawbox width expression rendered the progress bar full from the
  start (replaced with stepped boxes).
