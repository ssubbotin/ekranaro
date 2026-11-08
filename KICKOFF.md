# Ekranaro — kickoff brief for Claude Code

This file is the starting instruction for an AI coding agent (Claude Code) picking up the project at the design stage. Read it fully, then the linked documents, before producing anything.

## What exists

- [README.md](README.md) — one-page summary of the product
- [docs/vision.md](docs/vision.md) — the vision deck, text version (13 slides, speaker notes)
- [docs/plan.md](docs/plan.md) — problem, landscape, concept, architecture sketch, stack sketch, 4-phase roadmap
- [docs/decisions.md](docs/decisions.md) — decisions already taken (name, look, windowing model, modes, switching)
- `mockups/` — interactive prototype of the in-headset view, vision deck, landing-page mockup, screenshots

Everything in `docs/` is a **vision**, not a validated design. The architecture and stack in `docs/plan.md` are first guesses made without prototyping. Your job is to turn them into a plan that a solo developer can start executing, and to challenge them where they are wrong.

## Constraints that stand

These are decided and are not up for re-litigation unless you find a blocker (if you do, say so explicitly and propose the smallest change):

- Target hardware: **Meta Quest 3** (standalone), host: **Ubuntu 24.04 and 26.04** on Wayland.
- **Per-window** capture, not monitor mirroring. Each window is a numbered slot.
- Three modes — Focus, Deck, Wall — with the switching model described in `docs/plan.md`.
- Keyboard-first: physical keyboard through passthrough; no virtual keyboard in v1.
- **No PC-side VR runtime** (no SteamVR, Monado or WiVRn on the host). The host streams windows and data; spatial logic runs on the headset.
- Solo use only in v1 (no multi-user, no collaboration).
- License Apache-2.0. Open source from day one.
- Solo developer, evenings and weekends. Favour boring, well-documented technology over the technically ideal choice.

## Phase A — Research (produce `docs/research/`)

Investigate the following and write one short markdown file per topic, each ending with a recommendation and the evidence behind it (links, version numbers, dates checked). Use the web; do not rely on memory for anything that changes year to year.

1. **Per-window capture on Wayland, Ubuntu 24.04 and 26.04.** xdg-desktop-portal ScreenCast with per-window sources on GNOME and KDE: what actually works today, consent-dialog behaviour, restore tokens, cursor metadata, frame rates, DMA-BUF vs memfd. Alternatives if the portal path is insufficient (wlr-screencopy/ext-image-copy-capture, compositor-specific protocols, a custom compositor or nested compositor).
2. **Encoding.** H.265/H.264/AV1 hardware encode on Linux: VA-API (Intel/AMD), NVENC; GStreamer vs FFmpeg vs direct APIs; latency figures for low-latency presets; what the Quest 3 can hardware-decode (codecs, resolutions, number of simultaneous streams).
3. **Transport.** WebRTC (which stack: libwebrtc, webrtc-rs, GStreamer webrtcbin, str0m) vs QUIC/MoQ vs raw UDP with FEC. Latency, NAT/discovery over LAN, USB-C tethering options on Quest (adb reverse, USB networking), bitrate control per stream.
4. **Quest 3 client platform.** OpenXR on Quest: native C++/Vulkan vs Unity vs Godot vs Unreal vs WebXR. Hardware video decode into textures (MediaCodec → AHardwareBuffer → Vulkan), hand tracking API, passthrough API, eye-tracking availability (Quest 3 has none — confirm and plan the head-pose "gaze" proxy), text rendering quality for terminals in VR, Horizon OS store / App Lab requirements.
5. **Input injection on the host.** libei/libeis + xdg-desktop-portal RemoteDesktop vs uinput; keyboard layouts, modifier handling, pointer absolute positioning into a specific window.
6. **Prior art.** WayVR / WlxOverlay-S, Stardust XR, Immersed, ALVR, WiVRn, Moonlight/Sunshine, xrdesktop: what they solved, what we can reuse (code, protocols, lessons), licenses.
7. **Alert collectors.** GitHub Actions, GitLab CI, Jenkins, Prometheus Alertmanager APIs; polling vs webhooks on a LAN; a minimal shared alert model.

Keep each file under ~600 words. Where you tested something, say what and on which versions.

## Phase B — Architecture and stack (produce `docs/architecture.md` and `docs/stack.md`)

Based on Phase A:

- Propose the component architecture (agent, client, protocol). Include a diagram (Mermaid is fine), the data flows for a frame, a keystroke, an alert, and a mode switch, and a latency budget per hop.
- Recommend **languages and toolchains** for the agent and the client, with at least one serious alternative each and the reason it lost. Cover: build system, package layout, testing approach, CI (GitHub Actions), how the Quest APK is built and sideloaded, how the agent is packaged (.deb first, snap later).
- Define the **spec** that lives between them: slot model, mode model, alert model, pairing and discovery, stream negotiation. Enough for a second client or agent to be written against it. Put it in `spec/` as versioned markdown.
- List the **risks** that could sink the project (e.g. per-window capture gaps on a given compositor, Quest decode limits, text legibility) and for each the cheapest experiment that retires it.

## Phase C — Plan (produce `docs/plan-v0.1.md` and GitHub issues)

- Replace the 4-phase sketch with a concrete plan for **v0.1 = "stream and type"**: one Ubuntu window visible and readable in Focus mode on the Quest, keyboard and mouse working, under ~30 ms glass-to-glass on LAN. Nothing else.
- Break it into milestones of about one to two weekends each, ordered so that the riskiest experiments from Phase B happen first (spike before build).
- Define the "done" demo for v0.1 and the measurements that prove it (latency method, frame rate, legibility test with a real terminal font size).
- Create the repository skeleton (directories, README stubs, CI workflow that builds both sides, LICENSE, CONTRIBUTING, issue templates) and open one GitHub issue per milestone with acceptance criteria. Do not write feature code in this phase beyond what spikes require.

## Working rules

- Work in a branch and open a PR per phase; summarise findings in the PR description.
- When a decision in `docs/decisions.md` is overturned by evidence, record the new decision there with the date and the reason; never silently diverge.
- Prefer small spikes that produce a number (latency, fps, CPU %) over long write-ups.
- Ask the maintainer before adding a dependency with a non-permissive license or a cloud service.
- Keep the terminal-native look from `docs/decisions.md` for any UI you build, including the mockups' palette and fonts.

## Deliverables checklist

- [ ] `docs/research/*.md` (7 topics)
- [ ] `docs/architecture.md`
- [ ] `docs/stack.md`
- [ ] `spec/` v0 protocol documents
- [ ] `docs/plan-v0.1.md`
- [ ] Repository skeleton + CI + issues for v0.1 milestones
- [ ] Updated `docs/decisions.md`
