# Full Pipeline (12 steps)

The detailed reference pipeline. The 5-step v2 in SKILL.md is the current process; this is the detailed reference.

## 0. Setup (one-time)
- ffmpeg 8.x with libass: `ffmpeg -hide_banner -h filter=ass | head -3`
- Python deps in `~/workspace/.pylibs` (`pip install --target ~/workspace/.pylibs pillow faster-whisper`); `export PYTHONPATH=~/workspace/.pylibs` every shell. Never install to system paths.
- faster-whisper models in `~/.cache/huggingface`; re-download interactively after VM restart (egress proxy blocks background fetches). Clear `no_proxy` to bare `localhost,127.0.0.1` or httpx chokes on `::1`.
- CJK font: Noto Sans CJK (for libass).
- `/tmp` is wiped on restart — never deliver from it.

## 1. Kickoff (3 questions, none skippable unless already answered)
① Aspect 9:16/16:9 ② Card style (white Meta default / watercolor / inkline / editorial mix) ③ Main style (joshua / Dominic / card-only). Recommend; the creator decides. (Dark cards are legacy/opt-in only — do not offer as the default.)

## 2. Ingest
- `~/workspace/video-<topic>/`, source → `src.mp4`.
- `ffprobe` for res/duration/fps; extract a frame to check face position.
- Large sources: chat attachment ~100MB cap; Mac direct pull 50MB/file; otherwise cloud-drive link.

## 3. Transcribe
- `ffmpeg -i src.mp4 -ar 16000 -ac 1 audio16k.wav` → `transcribe.py` → word-level `asr.json`. **Chinese speech: `language='zh'`** (forcing 'en' produces garbage).
- Trust timing, not words (ASR text has errors). If a script was provided, cross-check brand names and technical terms against it. Send the transcript to the creator for verification BEFORE subtitling — without a script, this becomes sentence-by-sentence proofreading.

## 4. Segments
- Map script → time windows: which parts get graphics vs. pure talking head. Save as `segments.json`.
- Rhythm: joshua ~4s per graphic swap; Dominic ~2.2s per cut. In this pipeline: a visual change every 2–3s regardless; full-screen graphic pages hold ~5s max before cutting back to the person.
- Scan for motion dead zones NOW: any planned stretch of a few seconds with zero animation gets a sticker, a pop, or a small content-drawn animation from the narration.
- Mark enumeration moments (parallel item lists) for pop-in bubble/panel builds; mark split-screen moments where the narration literally refers to showing something on the other half.

## 5. Graphics
- Cards / hook / beats / dark anims per `animations.md`. Rendered as MP4 segments or PNG sequences.

## 6. Subtitles (`subs_full.ass`)
- PlayRes 1080×1920, styles CN (52px bold) / EN (40px), `MarginV` 400.
- One Dialogue per cue: `中文第一行\N中文第二行{\fs28\c&HAAAAAA&}{\rEN}English line one\NEnglish line two`.
- Proofread brands/English/homophones manually.

## 7. SFX
- Only approved pops. `adelay` instances mixed via `amix=normalize=0`. Volumes: hook ~0.55, card entrances ~0.4, beats ~0.28, typewriter ticks ~0.30.

## 8. Composite (`build_full.py`)
- Single `filter_complex`: trim/atrim per segment, `setpts`/`asetpts`, overlays, `subtitles=` burn, `amix` audio.
- **Sync iron rule**: no `-ss` input seeking + `trim=start=absolute` — input seeking zeroes timestamps and desyncs A/V. Single input, cut inside the filter graph.

## 9. QA (every time)
- `ffmpeg -ss <t> -i final.mp4 -frames:v 1 qa_<t>.jpg` at each segment-type midpoint. Check: face clear, no text-over-face, subtitles clear of graphics, no black flashes, B-roll privacy blurs intact, audio levels stable (`volumedetect` on head/mid/tail).
- Verify pacing: no full-screen graphic stretch longer than ~5s without the person; no motion dead zones (watch for any few-second stretch with zero animation, including pure talking parts).
- Verify overlay type: handwritten font (not heavy bold sans) over the person; white-border stickers; no circular avatar PiP.
- `blackdetect` scan for unintended black holds.

## 10. Deliver
- `~/workspace/your_files/<topic>-final-vN.mp4`, attached in the same message, results-only wording.

## 11. Revisions
- Small sample first (2 scenes), then full re-render. Bump filename version (v2 → v3) to defeat client caching.
- Record every correction as a preference; an overruled decision becomes a remembered rule.

## 12. Memory
- Update `~/MEMORY.md` with new preferences, decisions, and the delivered filename. Keep it tight — durable facts only.
