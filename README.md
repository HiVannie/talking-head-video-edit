# talking-head-video-edit

Full-pipeline editing for talking-head videos — from raw footage to publish-ready MP4.

This is the exact pipeline I use for my own bilingual (Chinese/English) talking-head channel: transcription with human verification, kinetic typography hooks, white info cards and split-screen reveals, on-camera overlays, B-roll beats with privacy blurring, bilingual subtitles, and a strict QA checklist.

> **Language note**: the reference implementation uses Chinese/English subtitles, but the pipeline works for any language pair — see *Adapting to other languages* below.

## How does it work?

You hand over a **source video** (you talking to camera). A **script** helps with accuracy but is optional — the pipeline transcribes your speech automatically. The pipeline takes it from there in five stages:

### 1. Ingest & kickoff
Your footage goes into a project folder untouched. Three quick questions lock the creative direction: aspect ratio (9:16/16:9), card style, and main format (knowledge explainer / opinion monologue / card-only). If you pre-trimmed the video yourself, that cut is treated as final — the pipeline never re-cuts your words.

### 2. Transcribe & verify
The audio is transcribed word-by-word with timestamps (faster-whisper). If you provided a script, it's used as a reference for brand names and technical terms. You get the transcript to check **before** anything is burned in — because creators ad-lib, and the subtitles must match what was actually said.

### 3. Graphics
This is where the video comes alive. Six animation systems, all generated frame-accurately with Python/Pillow:

- **Hook** — kinetic typography over your opening shot. Keywords type out character-by-character synced to your speech, with popups (logos, mascots, topic grids) that spring in without covering your face.
- **White cards & split-screen** — clean Meta-style info cards (centered title, bullets, footer pill). The card slides in from the top (0.4s ease-out) while you keep talking below in a full-width crop with a gentle 1.15× punch-in — the split-screen literally shows what you're mentioning. Use it especially when your narration says the other half of the screen should show the mentioned content.
- **On-camera overlays** — you stay full-frame while playful handwritten type (ZCOOL KuaiLe for CJK, slight −1.2° tilt) appears in the upper third with a soft shadow, white-border image stickers pop in at chest level, and key numbers go big in yellow handwriting. Vary the method — typewriter above the head vs. a pulled-down card — never one trick for the whole video.
- **Editorial explainer pages** — full-screen animated "editorial paper" pages for concepts/lists/comparisons: serif headline building word by word, elements staggering in one per phrase, cream ↔ near-black chapter flips with hard cuts. Hold only a few seconds (~5s max), then hard-cut back to you.
- **B-roll beats** — full-screen cutaways with slow Ken Burns motion. Faces, names, addresses, and phone numbers are blurred automatically before anything ships.
- **Legacy dark cards (opt-in only)** — the older glowing dark-canvas system still exists if a creator explicitly asks, but it is no longer the default.

Pacing rules apply to everything: a visual change every 2–3s, no motion dead zones even in pure talking stretches (small content-drawn animations fill gaps — e.g. an editing timeline when you talk about editing), and enumerated lists pop in one by one in a bubble/panel, accumulating in sync with speech.

### 4. Transitions & sound
Crossfades or hard cuts only — no black flashes, no 3D flips. Sound effects come from a tiny approved library (two pop variants); each one is placed deliberately on a visual beat, never sprinkled for show. Your voice is never filtered, normalized, or "enhanced" — what you recorded is what ships.

### 5. Composite, QA & delivery
Everything is assembled in a single ffmpeg pass: video segments, burned-in subtitles, and mixed audio. Then every segment type gets a frame-grab QA check (face clear? text clear of face? subtitles clear of graphics? privacy blurs intact? audio levels stable?). Only then does the MP4 ship.

## Features

- **Bilingual subtitles** — two languages in one subtitle track (primary on top, English below), positioned in the platform safe zone (TikTok bottom nav never covers them)
- **Kinetic typography hooks** — typewriter reveals synced to speech, with spring-in popups
- **White cards & split-screen** — clean centered cards sliding in from the top over your talking shot, bullets typing out
- **On-camera overlays** — handwritten type in the upper third, white-border stickers at chest level, yellow handwritten emphasis numbers
- **Creator-presence pacing** — full-screen graphic pages hold ~5s max, then cut back; a visual change every 2–3s, no motion dead zones
- **Enumeration pop-ins** — listed items pop in one by one in a bubble/panel, accumulating in sync with speech
- **Editorial explainer pages** — animated paper-style pages for concepts/lists/comparisons with hard-cut chapter flips
- **Privacy-safe B-roll** — automatic blurring of names, addresses, phones, company identifiers
- **Untouched audio** — your voice ships exactly as recorded; SFX mixed without level drift
- **Frame-level QA** — every segment type visually verified before delivery

## How to use it

### As an AI skill
Load `SKILL.md` into your AI assistant. Then just say:

> "Here's my video and script — edit it."

The skill drives the whole pipeline: it asks the three kickoff questions, transcribes, sends you the transcript for verification, builds graphics, composites, QAs, and delivers the MP4.

### Project layout
```
video-<topic>/
  src.mp4              # your source (never modified)
  scripts/             # build scripts (transcribe, make_hook, make_anims, build_full, ...)
  subs_full.ass        # bilingual subtitles (primary on top, English below)
  sfx/                 # your approved sound effects
  broll/               # B-roll sources + privacy-safe versions
  hook_frames/         # rendered hook PNG sequence (transparent)
  cardN_anim.mp4 / beat_*.mp4 / animNd.mp4  # graphic segments
your_files/<topic>-final-vN.mp4  # the deliverable
```

### Revision workflow
For layout changes: cut a small sample (first 2 scenes) for approval before the full re-render. Bump the filename version (`-v2` → `-v3`) on re-deliveries so players don't serve a cached old cut.

## What's inside

- **`SKILL.md`** — the operational core: 5-step workflow, project layout, operating rules. Load this to run the pipeline.
- **`references/pipeline.md`** — the full 12-step detailed pipeline (environment setup, transcription, segments, graphics, subtitles, SFX, composite, QA, delivery, revisions).
- **`references/animations.md`** — animation recipes: hook typewriter, white cards & split-screen, on-camera overlays, editorial pages, B-roll beats, number charts (and the legacy dark-card recipe, opt-in only). Includes timing values, SFX sync patterns, and production gotchas.
- **`references/hard-rules.md`** — red lines (audio untouched, no black frames, subtitle safety zones, privacy blurring) and creator preferences.

## Requirements

- ffmpeg with libass
- Python 3 + Pillow (for frame-accurate graphic generation)
- faster-whisper (for transcription; use `language='zh'` for Chinese speech)
- Noto Sans CJK or equivalent (for subtitle rendering)

## Adapting to other languages

The subtitle system is language-agnostic. To use a different language pair:

1. **Transcription**: set faster-whisper's `language` parameter (e.g. `language='es'` for Spanish, `'ja'` for Japanese). The current default is `'zh'` for Chinese.
2. **Subtitle styles**: `subs_full.ass` defines two styles (primary + English). Rename/restyle them for your pair — the one-Dialogue-event + style-switch pattern (`{\rEN}`) works for any two languages.
3. **Fonts**: swap Noto Sans CJK for a font covering your script.

Everything else (hooks, cards, B-roll, QA) is language-independent.

## License

MIT — use it, fork it, make it yours.
