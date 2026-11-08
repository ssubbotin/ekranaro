# Ekranaro — vision deck (text version)

Text extracted from the 13-slide vision deck in `mockups/vision-deck.html`; speaker notes are shown in italics.


## 1. Title

*Ekranaro: Esperanto for 'a collection of screens'. Open-source VR workspace for ops engineers, Ubuntu host, Quest 3 client.*

**OPEN SOURCE · VISION**

- Ekranaro
- A cockpit of screens for DevOps work, on Meta Quest 3.
- ekran·aro — Esperanto, “a set of screens”

## 2. The ops workday

*Count what a typical on-call engineer keeps open. The screen is the bottleneck, not the tools.*

**The ops workday: many small windows, one pair of eyes**

- 8–20
- terminals and SSH sessions open at once
- 3–5
- CI pipelines and dashboards to watch during a rollout
- 2
- chat and mail apps that interrupt
- 1–2
- physical monitors to fit all of it on
- Alt-tab, tmux prefixes and workspace hotkeys exist because the glass is too small. A headset removes the glass limit, but current VR desktops only give you bigger glass.

## 3. Existing VR desktops

*Landscape as of Nov 2026. Virtual Desktop has no Linux host. Immersed supports Linux but virtual displays only on GNOME Wayland. WayVR and Stardust XR are the open-source Linux-native options but mirror screens, not workflows.*

**Existing VR desktops and where they stop**

- Product
- Ubuntu host
- Screens on Q3
- Unit of work
- License
- Virtual Desktop
- no
- 3
- monitor
- paid, closed
- Immersed
- partial
- 5
- monitor
- freemium, closed
- Horizon Workrooms
- no
- 3
- monitor
- free, closed
- WayVR (WlxOverlay-S)
- yes
- any
- screen / app
- open
- Stardust XR
- yes
- any
- window (3D shell)
- open, early
- Immersed: Linux virtual displays only on GNOME Wayland. WayVR and Stardust XR need a PC-side OpenXR runtime (Monado/WiVRn) and have no notion of ops workflows.

## 4. The gap

*Three things none of them do.*

**The gap: monitors in space, not workflows in space**

- 01
- They stream whole monitors. Ops work is per window: one terminal, one log, one pipeline.
- 02
- Switching means grabbing and dragging a panel. On a desk it is one keystroke.
- 03
- Nothing knows what an alert is. A failing job looks the same as a passing one until you look.

## 5. Concept

*Core model: every window is a numbered slot. Three spatial modes rearrange the same slots. Alerts change a slot's state.*

**Concept: windows, slots and three modes**

- Windows, not monitors.
- Each Ubuntu window is captured on its own and becomes a slot
- [1]…[9]
- .
- Three modes, same slots.
- Focus, Deck and Wall rearrange the slots around you. Nothing is lost between modes.
- Alerts find you.
- A slot can turn red from a log line, a CI result or a dashboard threshold, in any mode.
- Native panels where streaming is wasteful.
- CI board and alert feed render in the headset from APIs, not from pixels.
- F · Focus
- One slot large, neighbours peeking at the sides, the rest in a dock.
- D · Deck
- Five slots in an arc at reading distance, like tmux panes with depth.
- W · Wall
- Every slot on a wall of monitors, dimmed, except what is alerting.

## 6. Focus mode

*Default mode for typing. Side panes are glanceable; dwelling on one for 0.7 s pulls it forward.*

**Focus mode**

- ›
- One window at full reading size, straight ahead
- ›
- Previous and next slot angled at the sides, live but dimmed
- ›
- Remaining slots in a numbered dock below
- ›
- Physical keyboard visible through passthrough

## 7. Deck mode

*For rollouts: deploy terminal centre, logs and dashboard beside it.*

**Deck mode**

- ›
- Five slots in an arc, all readable without turning the head far
- ›
- Swipe or arrow keys rotate the deck one slot
- ›
- Meant for rollouts: deploy, logs, dashboard, CI, chat side by side

## 8. Wall mode

*The 'wall of monitors'. Everything dimmed to 50% except alerting slots, which glow red. Pinching a red tile acknowledges and focuses it.*

**Wall mode**

- ›
- Every slot visible at once on a curved wall
- ›
- Quiet tiles dimmed; alerting tiles at full brightness with a red frame
- ›
- Alert sources: log patterns, CI results, dashboard thresholds, chat mentions
- ›
- Pinch an alerting tile to acknowledge it and drop into Focus

## 9. Switching

*Target: any window in under one second, hands on the keyboard. Four input paths, all mapped to the same slot model.*

**Switching in under a second**

- Super+1…9
- Jump to slot. Super+K opens a fuzzy finder over window titles. Same muscle memory as i3 or tmux.
- ≈ 0.2 s
- Glance
- Look at a side pane and it brightens. Hold the gaze 0.7 s and it comes forward. Hands never leave the keys.
- ≈ 0.9 s
- Pinch · swipe
- Pinch a tile to pull it to Focus. Swipe to rotate the Deck. Hand tracking, no controllers.
- ≈ 0.6 s
- F · D · W
- Mode keys. Tab cycles. The wrist panel offers the same three buttons for hands-only use.
- ≈ 0.5 s

## 10. Architecture

*Two components. Host agent on Ubuntu does capture, encode, input and collectors. Quest client is a native OpenXR app. No PC-side VR runtime needed, unlike WayVR/Stardust.*

**Architecture: Ubuntu host agent and Quest client**

- ekranaro-agent · Ubuntu 24.04
- per-window capture — xdg-desktop-portal + PipeWire
- encode — H.265 via VA-API / NVENC, one stream per slot
- input — libei / uinput keyboard and pointer injection
- collectors — CI APIs, Prometheus alerts, log patterns
- discovery — mDNS, pairing by QR
- ⟷
- Wi-Fi 6E or USB-C
- WebRTC / QUIC
- ≈ 10–20 ms
- ekranaro-quest · OpenXR app
- decode — hardware H.265 to textures, Vulkan
- slots, modes, dock, wrist panel
- hand tracking, gaze proxy from head pose, passthrough
- native panels — CI board, alert feed
- sideloaded APK, later App Lab
- No PC-side VR runtime. The host only streams windows and data; all spatial logic runs on the headset.

## 11. Stack choices

*Every piece is existing open tech; the project is integration plus the slot/mode UX.*

**Open stack choices**

- Need
- Choice
- Why
- Window capture
- PipeWire + portal
- Per-window, works on GNOME and KDE Wayland; the standard Ubuntu path
- Headset runtime
- OpenXR native
- Runs on the Quest itself, no SteamVR/Monado on the host
- Transport
- WebRTC or QUIC
- Low latency, NAT-friendly, per-stream bitrate control
- Languages
- Rust agent, C++/Rust client
- Same choices as WayVR and Stardust XR; shared contributors possible
- Text input
- Physical keyboard
- Passthrough shows the real keyboard; no virtual keyboard in v1
- License
- Apache-2.0
- Permissive for companies that want to ship it internally

## 12. Roadmap

*Four phases. Phase 1 is deliberately boring: stream windows, type into them.*

**Roadmap**

- PHASE 1 · 3 months
- Stream and type
- Agent captures windows, Quest shows them in Focus mode, keyboard and mouse pass through.
- PHASE 2 · 3 months
- Slots and modes
- Hotkeys, finder, Deck and Wall, glance dwell, hand pinch and swipe.
- PHASE 3 · 3 months
- Alerts and native panels
- Collectors for GitHub Actions, GitLab, Jenkins, Prometheus; CI board and alert feed in-headset.
- PHASE 4
- Polish and distribution
- Ubuntu .deb and snap, App Lab listing, saved layouts per project, other headsets via OpenXR.

## 13. Contribute

*Close on concrete asks.*

**How to contribute**

- Two repos, one spec. The slot and mode model is a small protocol document, so clients for other headsets and agents for other desktops can follow.
- github.com/ekranaro/agent
- github.com/ekranaro/quest
- github.com/ekranaro/spec
- GOOD FIRST ISSUES
- PipeWire per-window capture on KDE Plasma
- VA-API encode path for Intel iGPUs
- GitLab CI collector
- Log alert patterns as a shared ruleset
- Wrist panel layout for left-handed use
