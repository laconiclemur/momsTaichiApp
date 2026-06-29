# Grounded

**Tai Chi Walking. One step at a time.**

A gentle, audio-guided practice companion for older adults learning Tai Chi Walking at home. The app guides each session step by step — breathing, walking, timing — so the user never has to hold instructions in their head while moving.

### What you need to share it:

Send the .apk file directly (email, Google Drive, etc.)
The recipient enables unknown sources: Settings → Apps → Special app access → Install unknown apps
They open the file and tap Install

---

## What it does

- Guides three session types: Weeks 1–2 (foundation walking), Weeks 3–4 (posture and refinement), and Backward Walking
- Pre-recorded human voice leads each segment — no text-to-speech
- Screen dims during walking; single tap restores it
- Configurable walking loops with estimated session time shown before starting
- Randomised lift-test prompts during walking (suppressed on short sessions)
- Seamless audio loop with interrupt-and-resume for mid-session cues
- Soft chime at session close (baked into `sh_06.mp3`)

## Platform

Android (primary). Flutter project targeting API 36 (Android 16). Physical test device: Pixel 9.

## Getting started

```bash
flutter pub get
flutter run
```

Audio assets are in `00_assets/mp3/`. All files use snake_case names and are declared via directory glob in `pubspec.yaml`.

For a distributable build:

```bash
flutter build apk --release
```

Always run `flutter clean` after re-recording or replacing any audio asset — incremental builds silently serve stale cached bytes.

## Project structure

```
lib/
  theme/app_theme.dart          — color tokens, typography, spacing, button styles
  models/session_model.dart     — WeekGroup enum, SessionPhase enum, session data
  providers/session_provider.dart
  services/
    audio_service.dart          — AudioPlayer management: play, loop, interrupt, pause/resume
    session_service.dart        — session state machine and slot-based walking engine
    storage_service.dart        — shared_preferences wrapper (rotation keys, onboarding flag)
  screens/
    onboarding/                 — splash, onboarding flow, audio intro screen
    home/home_screen.dart       — session entry, loop count picker
    session/session_screen.dart — main practice screen (timer ring, dim overlay, pause)
    complete/                   — session complete screen
    reference/                  — scrollable program reference card
    settings/settings_screen.dart

00_assets/mp3/                  — all audio assets (snake_case .mp3)
00_assets/mp3/durations.json    — per-file durations in seconds (used by ring counter)
00_reference/plans.md           — full product plan, design system, phase checklist
00_reference/estimates.md       — task time log
```

## Key dependencies

| Package | Purpose |
|---|---|
| `just_audio: ^0.10.5` | Playback, looping, gapless queue (0.10.x required for Android 16) |
| `audio_session: ^0.2.3` | Bluetooth A2DP routing detection |
| `flutter_riverpod: ^2.6.1` | State management |
| `shared_preferences: ^2.3.2` | Session log, user preferences |
| `wakelock_plus: ^1.2.8` | Keep screen on during sessions |
| `google_fonts: ^6.2.1` | Plus Jakarta Sans + Lora (fonts also bundled locally in `00_assets/`) |

## Audio notes

Bluetooth headphones are the most reliable audio route. The session engine applies `AudioSessionConfiguration.music()` only when BT A2DP is detected — applying it unconditionally breaks USB routing.

Cheap USB-to-3.5mm dongles (≤ ~$10) use aggressive silence detection that causes audible dropout during the breathing pauses in `sh_01.mp3`. This is a hardware constraint; a route-aware keepalive is implemented but cannot fully resolve it on the cheapest adapters. Recommended route order: BT headphones → quality USB-C adapter (≥ $25) → wired 3.5mm jack.

## Design

Hybrid of the Linen design system structure (4px grid, 56dp touch targets, corner radii) with the Grounded palette: warm linen off-white `#F5F0E8`, sage green `#7A9E7E`, terracotta `#C17F5A`, near-black `#2C2C2A`. Session active screen inverts to dark `#1A1A18` with amber `#D4956A` instruction text. Typography: Plus Jakarta Sans for UI, Lora for session instruction text, monospace for the timer.

---

*Built for women 65–80 who want to do Tai Chi, not use an app.*
