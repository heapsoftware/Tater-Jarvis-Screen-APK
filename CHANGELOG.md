# Changelog

All notable changes to the Jarvis Screen APK. Format: `MAJOR.MINOR.REVISION`
(REVISION = bug fixes / small item changes; MINOR = features; MAJOR = breaking).

## 1.0.0 — 2026-10-11 — Native display client (Phase 1) + Phase 2/3 scaffolds

**Features**
- Native HUD client for the jarvis_screen core: SSE stream (`/events`),
  initial `state` snapshot, live event application, and the REST command
  surface (`/api/command`, `/api/pin-check`, `/api/ping`).
- Arc reactor ported from the web rig's three.js scene: classic film, E5
  nested arcs, design3, plus the speckled and azure-haze dormant clock
  looks; spring positions, speak-envelope pulse, unlock build-in
  choreography, and denial (red) state.
- Lock screen with tap / PIN pad / face-scan unlock paths, ACCESS GRANTED /
  denial states, DISCONNECTED status, and the dormant clock. Face scan
  mirrors the web rig's scan semantics: per-screen `face_scan_mode`
  (continuous while locked, or tap sessions with auto-retries), with plain
  tap fallback when the Face ID runtime is off.
- Card renderer for text, chat console, chart, greeting, entity state,
  climate (± setpoint), camera snapshots, and music transport; browser /
  embed card types show an on-style native-client placeholder.
- First-run config form (server URL + screen key + optional token,
  persisted locally — no deployment details in the repo).
- Kiosk scaffolding: device admin, boot receiver, lock task start.
- Phase 2 (voice) and Phase 3 (wake word) scaffolds: documented interfaces,
  no shipping mic code until the core-side origin-identity decision lands.