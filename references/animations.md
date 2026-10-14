# Animation Recipes

Reusable patterns from a production bilingual talking-head project.

## Hook (opening kinetic typography)

**Pattern**: transparent PNG sequence overlaid on the talking head (`hook_frames/%04d.png`).
**Script**: `make_hook.py`

- Big keywords type out character-by-character (typewriter, ~0.10–0.14s/char) synced to speech.
- Style: plain white text, no gradient/glow/background (recommended default). Thin dark outline only if readability needs it — the reference production used pure white with zero stroke.
- Position: above head or below chin, NEVER over eyes/face.
- Popups (mascot, logo, quote grids) use spring scale-in (`ease-out-back`), positioned to avoid face. Images pop as white-border stickers — never as a circular avatar PiP of the creator (tried, rejected — looked odd).
- 8-item grids: 4 cols × 2 rows fits better than 2×4 for 9:16; keep clear of subtitle zone (bottom 400px).

**Timing rule**: hook is ~8s; every element must land on its spoken word (check `subs_full.ass` timestamps).

## White cards & split-screen (scenes without B-roll) — DEFAULT

**Pattern**: clean Meta-style info card on top, person below (`make_cards_v2.py` → `cardN_anim.mp4`).
**When**: scene has no B-roll. This replaced dark cards as the production default.

- Layout: card on top (1080×700 for a 1080×1920 canvas), person below as full-width crop (1080×1220, i.e. `crop=1080:1220:0:<top>`). Measure the person shot's face position per video and set the crop offset so the subtitle zone stays clear of the face.
- Animation: card slides in from the top (0.4s ease-out) → bullet typewriter (~14 chars/s, blue cursor) → slides out at scene end. Person punch-in 1.15× and shift down, synced to the card entrance (0.4s ease), so the head stays fully visible — never cut off at the card edge.
- **Use it literally**: when the narration says the other half of the screen should show the mentioned content ("as I say this point, the other half of the screen shows it"), cut to this split-screen and actually show it. Not deprecated.
- SFX: a light pop on card entrance (one of the two approved pop variants); never whoosh.

### Legacy: dark-card segments (opt-in only)

Kept only for creators who explicitly ask. Full-screen dark video with staggered reveals (`make_anims_v2.py` → `animNd.mp4`).
**When**: only if the creator explicitly requests dark cards — otherwise use white cards above.

Elements, in order:
1. Small pill label top-left (e.g. "Muse") — fades in at 0–0.3s.
2. Title — typewriter at 0.3s (~0.15s/char), accent color on keyword.
3. Content box — rounded rect with glowing border, fades + rises in at 1.5–1.8s.
4. Flow indicator (optional) — animated dots/line showing a process (e.g. phone → GitHub), loops for the segment duration.
5. Bilingual caption bottom — fades in with the content box.

**Critical**: first text visible within 0.3s of segment start. Background has slow-drifting glow orbs so no frame is fully static.

**SFX**: pop on each element appearance. In `build_full.py`, add delayed pop instances:
```python
for tt in [84.8, 86.3, 110.3, 111.5]:  # absolute seconds
    sfx.append(f"[17:a]adelay={int(tt*1000)}|{int(tt*1000)},volume=0.30[tx{int(tt*10)}]")
```
And include every `[tx...]` label in the `amix` input list — a missing label fails the whole build (`rc: 234`).

## B-roll beats

**Pattern**: full-screen cutaway with subtle motion (`beat_*.mp4` via `make_beats*.py`).
- Source image → Ken Burns (slow zoom/pan) or gentle scale pulse.
- Privacy: run `blur_broll.py` first; blur names, addresses, phones, company identifiers. Heavier is safer — she asked twice for more blurring on the government form.
- Subtitles burn on top (from `subs_full.ass`).

## Cards (white Meta style)

**Pattern**: `make_cards_v2.py` → `cardN_anim.mp4`.
- White card, blue→purple gradient hairline on top, SCENE number in Meta blue, centered dark-gray title, gray EN subtitle, blue-dot bullets, blue pill footer — all centered.
- Animate: slide in from top (0.4s ease-out) → bullet typewriter (~14 chars/s, blue cursor) → slide out at scene end.
- Person punch-in 1.15× synced to card entrance, head fully visible (NOT during the opening hook — hook keeps original framing 1:1).
- Same card component doubles as the split-screen top card (see "White cards & split-screen" above).

## On-camera overlays (person stays full-frame)

**Pattern**: transparent PNG sequence composited directly over the talking head (person is never reframed away).

- Type: playful handwritten font (ZCOOL KuaiLe for CJK), slight ~−1.2° tilt. Pure-white for hook-style keywords; large yellow handwritten type for key numbers/emphasis words (e.g. "7%", "80%"). **Never heavy bold sans over the person** — it reads stiff and old-fashioned.
- Position: typewriter text in the upper third with a soft drop shadow, no boxes or backgrounds; positioned to avoid face and eyes.
- Images: white-border stickers (logo, mascot, reference grabs) popping in at chest level with a slight wobble — never circular avatar PiP of the creator.
- **Vary the method**: alternate between typewriter-above-the-head, pulled-down card, and sticker pops — never one technique for the whole video. A static full-screen text card is not a substitute.

## Enumeration pop-ins

**When**: the narration lists parallel items (e.g. "subtitles, animation, sound effects, materials").
**Pattern**: one bubble/panel near the person; each item pops in as the speaker says it and stays — accumulating, never replacing. All items remain visible at the end of the list.

## Sticker economy & content-drawn animations

- Don't keep reusing just a mascot + logo sticker, and never force an ill-fitting sticker to fill space. If no brand sticker fits the moment, **draw a small animation from the narration itself**: e.g. an editing timeline with a playhead sweeping across when talking about editing, a red ✗ on a stalled clip when the narration says it wasn't working, a speech-bubble waveform for audio.
- These micro-animations are also the fill for the no-dead-zone rule below.

## Creator-presence & pacing rules (apply everywhere)

- Full-screen graphic/editorial pages hold only a few seconds (**~5s max**) before hard-cutting back to the person. Person and graphics alternate through the video.
- A visual change every **2–3s**, enforced even in pure talking stretches: no segment may sit static for several seconds. If nothing needs visualizing, add a sticker, a pop, or a small content-drawn animation — an empty frame is never acceptable (a few seconds of bare talking head reads as a dead zone).
- These rules are the same principle as "no black frames" applied to motion.

## Editorial explainer pages (reference style)

**Reference**: full-screen animated explainer reels by sanjai.builds on Instagram (Dots/Muse/Grok comparison; Figma Make vs ChatGPT vs Claude test), frame-analyzed. No presenter — the pages themselves carry the narration. This recipe borrows that animation language and rhythm for talking-head videos.

**Look — "editorial paper"** (hex approximations read from frames, close enough to recreate):
- Background alternates per chapter: warm cream `#F2EFE9` (range #EFEDE8–#F3F1EB) ↔ near-black `#0E0E10`. Flat matte, never pure white; subtle vignette darkening at top/bottom edges. The light/dark full-page flip IS the chapter transition — hard cuts only, no slides/zooms/dissolves.
- Text: near-black `#1B1B1B` on light pages, white on dark; secondary/micro text gray `#8E8E93` (light) / `#A8A8A8` (dark).
- Headline: large transitional serif (Didone-like: Playfair Display / Editorial New; CJK: Noto Serif CJK), ~500–600 weight, roman + italic mixed. ONE word per headline in a colored script/italic accent — red `#E5484D`, blue `#2F7CF6`, green `#34C759`, orange `#E8622C`, salmon `#E8836B`, purple `#8B6FD8`, or a pink→orange→purple gradient. Accent never covers a whole sentence.
- Elements: white `#FFFFFF` cards (radius ~18–24px on 1080-wide canvas, soft neutral shadow) holding chat bubbles, app rows, calendar tiles (blue `#2F7CF6` date block), notification cards, tab rows (active tab filled `#2F7CF6`); pill tags; black micro-badges (solid black pill, white micro-text, e.g. "∞ REPEATS"); thin dotted connector lines (light gray, ~2–3px dots); circular avatar chips; skeleton placeholder bars `#E6E3DC`; photo cards with dark scrim + serif overlay text; green-check `#34C759` / red-X `#E5484D` icon badges. Small geometric sans for labels/pills/captions. Crisp digital lines, no hand-drawn texture.

**Page layout (9:16, 1080×1920)** — strictly centered composition, side margins ~90–120px (content ≈ 75–80% of width), one idea per page, only 3–7 elements, generous negative space:
- Top ~12%: chapter pill-nav, centered (`01 … / 02 … / 03 …`, small uppercase sans, wide letter-spacing, ~22–26px; active pill = black fill + white text on light pages, white fill + black text on dark; inactive = transparent with thin gray outline `#C9C7C1`).
- ~15–35%: micro kicker (uppercase, ~0.15–0.2em tracking, ~20–26px, gray) directly above the headline; headline block starts ~180–220px from top, ~100–130px type, line-height ~1.05–1.15, 2–4 short lines, centered.
- ~35–80%: the animated artifact — cards, comparison table (avatar columns × rows), node diagram, wireframe (dashed outline, dimension label like "390 × 844" in micro-type). Card-internal text is left-aligned; everything else centered.
- ~85–92%: caption strip, centered, max 2 lines, ~60px clear of the bottom edge (in the reference: bold sans karaoke, ~36–44px).

**Entrance grammar (the core of the style)**:
- Headline appears WORD BY WORD (~0.25–0.4s per word), lip-synced; the accent word pops in slightly after its neighbors.
- Body elements stagger in one every ~0.5–1s, each triggered by the next narration phrase: cards slide up + fade; pills pop with a tiny scale bounce; dotted lines DRAW progressively node→node (~1s); table rows fill left→right as skeleton bars, then resolve into real content.
- Text/prompt cards type character-by-character with a blinking caret (~30–50ms/char).
- Wireframes build progressively: dashed outline → skeleton blocks → filled content → floating pill tags pop in around it; checkmarks/dimensions annotate last.
- **Accumulation, not replacement**: elements stay; the page builds up. Never clear the page mid-build.

**Rhythm**: page hold ~4–7s (longer on artifact pages so the build finishes); new reveal every ~1–2s, one per narration phrase; chapter change = hard cut + background flip.

**Talking-head adaptation**:
- The creator stays full-screen talking; cut to an editorial page only while explaining a concept/list/comparison, then hard-cut back. Cap each page at ~5s — split longer explanations across multiple short pages interleaved with the person (in the original no-presenter reels, 4–7s holds are fine; inside talking-head videos they aren't).
- Chapter pill-nav maps to scene pills (`01 … · 02 …`), active = current scene.
- Keep the production's subtitle format burned over the page's bottom zone (the reference uses bold-sans karaoke word-by-word captions — an alternative if the format allows single-language captions).
- Pick a default page tone (near-black pages suit modern setups; cream is the contrast flip) and stay consistent within a video.
- On editorial pages, the creator's mascot may sit at a page corner as a silent host (optional, per creator approval) — this is a mascot sticker, NOT a circular face avatar.

## Build & composite

`build_full.py` assembles: `[src][cards][beats][anims][sfx][hook_frames]` via one `filter_complex`.
- Video: `x264`/`libx264`, `yuv420p`, 1080×1920.
- Audio: voice = `atrim`/`asetpts` only; SFX mixed with `amix=normalize=0`.
- Subtitles burned with `ass` filter from `subs_full.ass`.

**Copy-paste gotcha**: `cp -r /tmp/hook hook_frames` does NOT replace an existing dir — it nests. Always `rm -rf hook_frames` first.

## Number/data presentation (chart style)

**Reference**: 大方BigFang, "Will Bitcoin Really Hit $1 Million Per Coin?" (YouTube), frame-analyzed. A dedicated style for presenting NUMBERS in finance/data explainers. The reference is 16:9; the proportions below adapt to 9:16 (hero number ≈ 15–20% of frame height; chart in the middle band; keep the subtitle safe zone clear).
**⚠️ Sound design NOT verified in the source analysis** (the review environment had no audio). Verify against the original before shipping sound for this style; the visual recipe below is fully verified.

**Look**:
- Full-bleed mustard-yellow vertical gradient `#F7B500` (top) → `#FFD23F` (bottom); a pure-black variant is used for hook cards. Ink is `#111111`.
- Hero numbers: extra-bold rounded geometric sans (Nunito ExtraBold / Baloo-like; CJK: heavy rounded bold), pure black, HUGE (~15–20% of frame height), tilted −2° to −4°, thousands separators.
- Icons: flat black silhouettes with soft drop shadows (people crowds = "demand" metaphor, gold bars); subject icon as orange `#F7931A` circle with white glyph, or black circle with gold glyph.
- Grid: minimal dashed gray-beige lines (`#C9B896`, ~2px), one thick black axis bar at the far left; no axis labels.

**Signature moves**:
1. **Odometer count-up** — the number ROLLS up fast (~1–2s, ease-out cubic), digits motion-blurred mid-roll, lands with a ~2-frame settle (no overshoot bounce).
2. **Self-drawing line chart** — very thick black stroke (~14px at 1080 wide, slight hand-drawn wobble) draws left→right over ~3s; a glowing white pen-tip dot with soft halo rides the leading edge. Grid fades in first (~0.5s).
3. **Synced counter** — while the line climbs, the hero number below counts up in sync; after it lands, a thick black underline bar draws beneath it left→right (~0.6s).
4. **Peak marker** — at the peak, the subject icon in a circle pops in (~0.25s scale-up); a brief variant emits thin white concentric ripple rings around the icon.
5. **Comparison pair** — two items side by side, icon + label + hero number each, same type treatment.
6. **Proportion grid** — icons in a grid appear row by row; ghosted/translucent vs solid encodes the ratio.
7. **Section punctuation** — full-screen color card with one huge tilted number/word between sections.

**Rhythm**: each data graphic holds ~4–8s, then a HARD CUT back to the talking head. No wipes/dissolves. The reference burns captions as a black translucent rounded bar (white bold primary line over smaller secondary line).
