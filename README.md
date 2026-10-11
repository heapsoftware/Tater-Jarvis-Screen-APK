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

## Roadmap — remaining full-port features

Phase 1 is display-complete; the browser client's interactive/audio-
heavy surface is still to port, in this order:

- Camera-event popups (events already parsed; popup overlay UI pending)
- Live MJPEG camera feeds (snapshot-only today)
- TTS + music audio playback (captions + reactor pulse already work)
- Voice input: tap-to-talk mic → `POST /api/voice`, follow-ups, mic
  capture tuning
- On-device wake word ("hey jarvis") — native ONNX scoring of the core's
  openWakeWord models instead of browser WASM
- Edit-mode placement editor
- Server-rendered embed cards (web / youtube / video / device camera)

See `CHANGELOG.md` for release history.
