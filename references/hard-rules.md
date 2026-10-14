# Hard Rules & Preferences

Violating any red-line item counts as an incident. Preferences guide defaults; the creator can override per video.

## Red lines
- **Audio**: source voice gets ZERO filters. Only `atrim`/`asetpts`. No `afftdn` (muffles), no `loudnorm`, no highpass. `amix` with `normalize=0` (default normalize causes volume drift).
- **Content lock**: CapCut pre-trimmed source = final. No cutting words, no compressing pauses, no reordering.
- **Subtitles**: must match ACTUAL speech (creators ad-lib; the script is reference only). Chinese speech → transcribe with `language='zh'`. One Dialogue event per cue with `{\rEN}` switch; `\N` for line breaks. `MarginV` 400 (9:16) / 90 (16:9). Brand names, English words, homophones: manual proofread pass, one error = incident.
- **No black frames**: max ~0.3s before first visual in any segment. No 2–3s black holds.
- **No motion dead zones**: no stretch of a few seconds with zero animation, including pure talking stretches. Keep a visual change every 2–3s — a sticker, pop, or small content-drawn animation. An empty frame is never acceptable.
- **Creator presence**: full-screen graphic/editorial pages hold ~5s max before cutting back to the person; person and graphics alternate. Never long stretches with no person on screen.
- **Face**: no text/graphic over eyes or face. Avatar crops keep head-to-chin fully inside. No circular avatar PiP of the creator (tried and rejected — looked odd); editorial-page avatar chips for compared products are fine.
- **Privacy**: blur every identifier in B-roll (names, addresses, phones, company names/IDs/URLs). Blur heavier rather than lighter.
- **Transitions**: crossfade or hard cut only. No black flash, no 3D flips.
- **SFX**: keep a minimal set (e.g. a dry pop and a bubble pop). Avoid whoosh/ding unless requested. SFX only where approved, at approved volume.

## Preferences
- 9:16 default. White Meta-style cards (centered everything), split-screen as the standard layout (card top, person below), on-camera overlays while the person talks. Bilingual CN-top/EN-bottom. Dark cards are legacy/opt-in only.
- Typewriter reveals for keywords (the "progress closure" feel). On-camera type uses a playful handwritten font (ZCOOL KuaiLe for CJK), slight ~−1.2° tilt — never heavy bold sans over the person (too stiff/old).
- Split-screen is a live tool: use it literally when the narration says the other half of the screen should show the mentioned content.
- Scenes without B-roll get white cards / split-screen / editorial pages (or B-roll-style cutaways), alternating with the person — never long person-less stretches.
- Enumerations: listed items pop in one by one in a bubble/panel, accumulating in sync with speech.
- Sticker economy: don't repeat just a mascot + logo, never force an ill-fitting sticker — draw small content-matched animations from the narration instead.
- B-roll = full-screen cutaway when available.
- Delivery: results-only, no jargon. MP4 attached in the same message. "Confirmed done + deliver", no step-by-step narration.
- Revisions: for layout changes, cut a small sample (first 2 scenes) first; full re-render only after she approves.
- If the creator does beauty filters themselves before handing over, received footage is assumed final.
- Retake selection: large objective gap → you decide; small gap or substantively different content → the creator decides. Always report what was cut/kept and why; an overruled decision becomes a remembered preference.
- Do NOT operate the creator's editing app (slow, fragile, token-expensive). Do NOT promise CapCut project export, Overdub voice clone, eye-contact correction, or green-screen keying — state limits plainly.

## File conventions
- Project: `~/workspace/video-<topic>/`, source as `src.mp4` (never modify).
- Deliverable: `~/workspace/your_files/<topic>-final-vN.mp4` (bump version for re-deliveries to avoid client caching confusion).
- Keep `script_8scenes.md`-style script notes, `subs_full.ass`, and QA frame grabs in the project dir.
