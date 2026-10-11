# Jarvis Screen APK (Geekland)

A native Android client for the [Tater Jarvis Screen](https://github.com/heapsoftware/Tater-Jarvis-Screen)
core — a native-first alternative to the browser HUD, designed for
Android 14 kiosk hardware (built against a Rockchip RK3576 kiosk
tablet), starting at boot via device-owner lock task.

No WebView wrapper: the HUD renders natively, consumes the core's SSE
state stream (`GET /events`) and REST command surface, and later phases
add on-device voice and wake-word detection.

## What it does (Phase 1)

- Streams the core's screen bus over SSE (`/events?screen=<key>` — initial
  `state` snapshot + live events) and applies the full event surface
  (lock / layout / cards / reactor / speak captions / alerts / console /
  music / camera snapshots).
- Renders the arc reactor natively: the classic film, E5 nested-arc and
  design3 looks, the speckled and azure-haze dormant clock looks, spring
  positioning, speak-envelope pulse breathing, unlock build-in
  choreography, and red denial state.
- Lock screen unlock paths: tap, 4-digit PIN pad (server-verified), and
  Face ID camera scans matching the web client's scan modes (continuous
  while locked, or tap sessions) with tap fallback when the Face ID
  runtime is off.
- Card grid at the payload's percent geometry with per-screen placement
  overrides; text / console / chart / greeting / entity-state / climate /
  camera / music cards render natively; browser-embed card types (web,
  youtube, video, device camera) show an on-style placeholder.
- First-run config form (server URL, screen key, optional `SCREEN_TOKEN`)
  persisted on-device; kiosk boot via device-owner lock task.

The server never ships data the browser wouldn't see; PIN codes are
verified server-side.

## Privacy

- Connection settings (server URL, screen key, token) are entered
  on-device at run time and stored only on the device; none are embedded
  in the APK or in these release notes.
- Face-scan frames are sent to the core's face-check endpoint and
  processed in memory; they are not stored by the app or the core.
- The APK declares only INTERNET, camera (for Face ID unlock), mic
  capture (reserved for the upcoming voice phase), and boot/kiosk
  permissions.

## Install

Sideload the release APK, then provision the kiosk:

```bash
adb install -r jarvis-screen.apk
adb shell dpm set-device-owner com.tater.jarvis.screen/.kiosk.DeviceAdmin
adb shell pm grant com.tater.jarvis.screen android.permission.CAMERA  # Face ID scan
```

The app opens in kiosk mode; run the built-in config form once
(server URL + screen key + optional token) to connect it to your
Tater Jarvis Screen core.

## Roadmap — build order

Phase 1 is display-complete. The rest, in work order — the phase each
item belongs to is tagged on the item:

- **SAT-linked wake-word reaction (launch requirement)** — most SAT
  behavior (actions routed to the linked screen; voice-turn captions +
  reply envelope pulses) already works through existing events. Remaining
  is the pre-reply part — the screen reacting as if IT picked up the wake
  word (wake animation + pulse from the utterance's `rms` envelope on the
  planned `voice_turn` event); visual-only, never double audio (first
  needs the core-side satellite-voice-mirror contract).
- **Camera-event popups** — `camera_popup` / `cam_snap` events are
  already parsed into state; popup overlay UI pending.
- **Live MJPEG camera feeds** — `ha_camera` cards are snapshot-only
  today.
- **Audio playback on the APK (Phase 2 entry)** — captions + reactor
  pulses already render; play the server-rendered TTS audio
  (`/api/audio/<ref>`) and act as an audio sink — including the
  per-screen success/fail/unlock voices.
- **Voice input (Phase 2)** — tap-to-talk mic → `POST /api/voice`
  (16 kHz PCM16, server-side STT → voice turn → TTS), follow-up
  conversations, per-screen mic capture tuning (echo cancellation / noise
  suppression / auto gain / rate / size). Blocked on the core-side
  origin-identity decision (APK turns need a distinct platform/origin
  from the browser's `webui`).
- **On-device wake word (Phase 3)** — "hey jarvis" running natively
  (ONNX Runtime / NDK over the core's openWakeWord models instead of
  browser WASM); the tablet becomes one more wake-point that may win
  pickup.
- **Edit-mode placement editor** — the browser's visual "Edit Layout on
  Screen" drag editor.
- **Server-rendered embed cards** — `web` / `youtube` / `video` /
  device-`camera` cards (incl. the headless-Chromium CDP pipeline) show
  an on-style native placeholder today.

See `CHANGELOG.md` for release history.