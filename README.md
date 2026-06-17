# FallMotion

Sovereign motion graphics in a single HTML file. The After Effects-lite of the FallStudio suite (phase 2). Prime **1447** · v1.0.0 · MIT.

One file. Zero dependencies. Works from `file://`. No build step. No telemetry. Your scene never leaves the device.

---

## For end-users

Open `index.html` in any modern browser (Chrome 113+ recommended). Build motion graphics: shape layers, text, images, on a timeline with keyframes and easing. Export to WEBM video, animated GIF, or SVG frame.

### What it does

- **Scene** — configurable canvas (default 1920×1080), background colour, frame rate, duration up to 60s.
- **Layers** — rectangles, ellipses, lines, text, images (uploaded → embedded base64).
- **Timeline** — every layer is a row. Click the diamond next to a property (x, y, scale, rotation, opacity) at the current playhead to drop a keyframe. Drag keyframes horizontally to retime.
- **Tweening** — values interpolate between keyframes. Per-keyframe easing: linear, ease-in, ease-out, ease-in-out.
- **Playback** — spacebar plays/pauses. Arrow keys step a frame. Scrub the ruler with the mouse. Loop toggle on the transport.
- **Templates** — logo reveal, lower third, slideshow, counter. One click to load.
- **Export** — WEBM (MediaRecorder, primary), GIF (inline encoder — see notes below), single-frame SVG, or full scene JSON.
- **Autosave** — every change is written to IndexedDB. Reopen the tab → your scene is still there.
- **Autopilot (Ω)** — `Ctrl+K` opens a command palette. Type intents like `logo reveal: Konomi`, `lower third Mansoor Khan Director`, or `fade in`. T0 (keywords) works offline. T3 (BYOK Claude/Gemini/GPT/OpenRouter via Settings) parses arbitrary phrases into scene actions.

### Keyboard

| Key | Action |
|---|---|
| `Ctrl+K` | Open Ω autopilot |
| `Space` | Play / pause |
| `←` / `→` | Step one frame |

### Export performance notes

- **WEBM** is the primary export — fast, hardware-encoded, real-time. The recommended path.
- **GIF** uses an inline GIF89a encoder (median-cut palette, Floyd-Steinberg dither, LZW). It is implemented from scratch with no library — accordingly it is **slow**. Keep duration short and frame rate modest: 5s @ 15fps (75 frames at 640px wide) is the comfortable upper bound. Anything larger will warn before encoding. If the encoder fails on a pathological scene it falls back to a frames ZIP (PNG per frame) so you can convert externally.
- **SVG** export is the current frame, fully vector, opens in any browser or vector editor.

---

## For developers

### Architecture

| Concern | Where |
|---|---|
| State | `state` global (scene, layers, time, settings) |
| Layer factory | `makeLayer(type)` |
| Property eval (keyframe + ease lookup) | `evalProp(layer, prop, t)` |
| Render to SVG | `renderFrame(t)` |
| Timeline + props UI | `UI.renderTimeline / renderProps` |
| Playback loop | `Player.play` (rAF-driven, wall-clock based) |
| Cascade routing | `Cascade.detectTier / generate` |
| Ω palette | `Palette.update / run / askT3` |
| GIF encoder | `GifEncoder.encode` + `BitStream` |
| ZIP fallback | `ZipWriter.create` |
| Persistence | `DB` (IndexedDB, `scenes` + `settings` stores) |

### Cascade tiers

- **T0** — offline keyword router (always works)
- **T2** — local Ollama detected on `127.0.0.1:11434` (best effort probe)
- **T3** — BYOK Anthropic / Gemini / OpenAI / OpenRouter via Settings (saved in IndexedDB, never transmitted elsewhere)

### Estate alignment

- `KONOMI` sovereign shim baked in (`window.KONOMI`)
- `fallmesh` BroadcastChannel announces on the `fall-signal` channel
- `postMessage` API responds to `{target:'fallmotion',action:'ping'|'getScene'}`
- PWA manifest as inline `data:` URL — installable from `file://`

### Mods

To change the brand palette: edit the `:root` CSS variables.
To add a property type: extend `props` in `makeLayer`, extend the property loop in `UI.renderProps`, then handle it in `renderFrame`.
To extend templates: add a branch in `Templates.apply`.

### The 14-point gate (FallStudio doctrine)

```
[x] 1 single HTML file · sovereign · file://
[x] 2 <400KB
[x] 3 L1 FACE · domain view (preview + timeline, not a generic dashboard)
[x] 4 L2 SWARM · Ω palette + named ops (new, load, save, export, templates, autopilot)
[x] 5 L3 CASCADE · T0 always · T2 probe · T3 BYOK
[x] 6 L4 BLOOM · keyword router + T3 intent parser
[x] 7 L5 PERSIST · IndexedDB autosave · JSON export covers everything
[x] 8 L6 SKIN · mobile-aware · estate dark palette · brass accent
[x] 9 L7 ASS · empty state nudges to add a layer · no wizard
[x] 10 Konomi licence shim baked
[x] 11 fallmesh BroadcastChannel · prime 1447
[x] 12 PWA manifest data: URL
[x] 13 README two-audience · MIT LICENSE
[x] 14 .nojekyll for Pages legacy
```

### FallStudio plan · phase 2

FallMotion sits alongside FallVector (vector editor), FallPDF (document forge), FallMage (image work), FallBrief (legal research) etc. The phase-2 suite shares the brass-on-void aesthetic, the Cascade tier model, the fallmesh BroadcastChannel, and the single-file deployment story.

### Licence

MIT. Copyright (c) 2026 Simon Gant · sjgant80-hub. Seal: ◊·κ=1.
