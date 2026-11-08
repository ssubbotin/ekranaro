# Ekranaro — decisions log

Decisions taken during the design stage (Nov 2026). Append new entries with a date and a reason; do not rewrite history.

## 2026-11 — Initial design decisions

- **Name:** Ekranaro (Esperanto: *ekrano* = screen, *-aro* = collection). Tagline: "a cockpit of screens for ops, on Quest 3".
- **Look:** terminal-native. Font DM Mono (headings, labels) and DM Sans (body). Palette: background `#0B0D10`, phosphor green `#7CFF9B` (accent), amber `#FFB347` (warn), red `#FF6B6B` (alert), text `#D6DCD8`, dim `#6B7570`.
- **Windowing model:** hybrid — per-window capture from Ubuntu (Wayland / PipeWire via xdg-desktop-portal) plus a few native in-headset panels (CI board, alert feed). Not monitor mirroring.
- **Users:** solo only in v1. No collaboration features.
- **Modes:** Focus / Deck (arc of 5) / Wall (all tiles, dimmed except alerts).
- **Switching:** numbered slot hotkeys (`Super+1…9`; the prototype uses `Alt+1…8` / `⌘K` as a flat-screen stand-in), `Super+K` or `/` fuzzy finder, glance (gaze dwell 0.7 s pulls a side pane forward), pinch (click) to pull a tile to Focus, swipe to rotate the deck.
- **Text input:** physical keyboard through passthrough; no virtual keyboard in v1.
- **Host / client split:** no PC-side VR runtime; host streams windows and data, spatial logic runs on the headset.
- **Stack sketch (unvalidated):** Rust agent on Ubuntu 24.04 and 26.04 (Wayland); OpenXR native client (C++/Rust) on Quest 3; H.265 via VA-API/NVENC; WebRTC or QUIC transport; libei/uinput input injection; mDNS + QR pairing. To be confirmed by the research in [KICKOFF.md](../KICKOFF.md).
- **License:** Apache-2.0.
- **Repositories:** `agent`, `quest`, `spec` planned; this repo holds vision, plans and mockups.
