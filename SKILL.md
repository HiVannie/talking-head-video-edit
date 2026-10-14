---
name: "talking-head-video-edit"
description: "Full-pipeline edit for talking-head videos: subtitle transcription, kinetic typography hooks, white cards, split-screen and on-camera overlays, B-roll beats, editorial pages, and final composite. Trigger when a creator sends a new video (script optional), or asks for video edits/revisions."
metadata: { "includeInPrompt": true }
---

# Talking Head Video Edit

## Purpose
End-to-end video editing for talking-head creators: they send a source video (a script helps but is optional — the pipeline transcribes automatically), you deliver a publish-ready MP4. The creator doesn't learn tools; you own subtitles, graphics, transitions, SFX, B-roll, and QA.

This is the exact pipeline used in production for a bilingual (Chinese/English) talking-head channel. The subtitle system supports any language pair (see README *Adapting to other languages*). Adapt the style tokens to your own brand.

## Workflow

1. **Ingest** — source MP4 + script text. Ask the 3 kickoff questions if not already answered: ① aspect (9:16/16:9) ② card style ③ main style (knowledge/explainer vs. opinion/monologue vs. card-only). If the creator pre-trimmed the footage, treat content as locked — do not cut words or compress pauses.
2. **Transcribe + verify** — word-level transcription (use `language='zh'` for Chinese speech). If the creator provided a script, use it as a reference for brand names and technical terms — but subtitle text must match actual speech (creators ad-lib). Send the transcript to the creator for verification BEFORE burning subtitles. No script? The verification step becomes sentence-by-sentence proofreading.
3. **Graphics** — white cards / split-screen / on-camera overlays / hook animations / B-roll beats per direction; full-screen editorial explainer pages (serif headline, build-up reveals, chapter pill-nav) are also available as a page style. Full-screen graphic pages hold only a few seconds (~5s max) before cutting back to the person — person and graphics alternate. While the person is on screen, vary the overlay method (typewriter above the head vs. a card pulled down), never one method throughout. See `references/animations.md`.
4. **Transitions + SFX** — crossfade or hard cut only (no black flash, no 3D). SFX only from an approved library, only where approved.
5. **Composite → QA → deliver** — assemble, QA every segment type with frame extracts, deliver MP4. Results-only language, no jargon.

For layout revisions: cut a small sample (first 2 scenes) for approval before full re-render.

## Project Layout
```
video-<topic>/
  src.mp4              # source (never modify)
  scripts/             # build scripts (transcribe, make_hook, make_anims, build_full, ...)
  subs_full.ass        # bilingual subtitles (primary lang on top, English below, \N line breaks)
  sfx/                 # approved SFX only
  broll/               # B-roll sources + privacy-safe versions
  hook_frames/         # rendered hook PNG sequence (transparent)
  cardN_anim.mp4 / beat_*.mp4 / animNd.mp4  # graphic segments
your_files/<topic>-final-vN.mp4  # deliverable
```

## Operating Rules
- **Audio**: source voice untouched — only `atrim`/`asetpts`. No denoise, no loudness normalization, no highpass (they alter voice character). `amix` must use `normalize=0`.
- **Content lock**: pre-trimmed source = final. No cutting, no pause compression.
- **Subtitles**: platform safe zone (`MarginV` 400 for 9:16 TikTok, 90 for 16:9). Bilingual = ONE Dialogue event with style switch (`{\rEN}`), never two overlapping events. Brand names / English words / homophones get a manual proofreading pass.
- **No black frames**: every second must have visual content. Card segments need text within ~0.3s of start plus motion or SFX; never a multi-second black hold.
- **No motion dead zones**: no stretch of a few seconds with zero animation, including pure talking stretches. Keep a visual change every 2–3s: a sticker, a small content-drawn animation, or a pop. When there's nothing to visualize, invent a small animation from the narration itself rather than leaving the frame empty.
- **Creator presence**: full-screen graphic/editorial pages may hold only a few seconds (~5s max) before cutting back to the person; person and graphics alternate throughout. Never long stretches with no person on screen.
- **Face safety**: big text never covers eyes/face. Avatar crops keep head-to-chin fully inside. No circular avatar PiP of the creator (tried and rejected — looked odd).
- **Cards**: default is white Meta-style cards, split-screen (card top 1080×700, person below as full-width 1080×1220 crop, card slides in from top 0.4s ease-out, person punch-in 1.15×), and on-camera overlays. Dark cards are legacy/opt-in only — use only if the creator explicitly asks. Use the split-screen literally when the narration says the other half of the screen should show the mentioned content.
- **On-camera overlay type**: playful handwritten font (ZCOOL KuaiLe for CJK), slight ~−1.2° tilt — never heavy bold sans over the person (reads stiff and old). Images on the person shot are white-border stickers popping in at chest level; key numbers/words can be large yellow handwritten type.
- **Enumerations**: when the narration lists parallel items, show the items as text in a bubble/panel, popping in one by one, accumulating in sync with speech.
- **Sticker economy**: don't keep reusing just a mascot + logo, and never force an ill-fitting sticker to fill space. Instead draw small content-matched animations from the narration itself (e.g. an editing timeline when talking about editing).
- **B-roll privacy**: blur all identifying info (names, addresses, phones, company IDs). Heavier is safer.
- **SFX**: keep a small approved library (e.g. two pop variants). No whoosh/ding unless the creator asks. Only where it matches on-screen action.
- **Delivery**: "confirmed done + result", no process narration. Bump filename version (v2 → v3) on re-deliveries to defeat client caching.
- **Environment**: `PYTHONPATH` for project-local Python deps, ffmpeg with libass, CJK fonts for subtitles. Never install to system paths on ephemeral VMs.

See `references/pipeline.md` for the full 12-step pipeline, `references/animations.md` for animation recipes, `references/hard-rules.md` for the complete rule list.
