# Changelog

All notable changes to FretboardTrainer are documented here. The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.0] - 2026-05-17

UX polish pass to make the app comfortable for non-technical users to install and operate.

### Added
- **Three new exercise modes joined the existing Trainer**: a frozen-pitch detection layer now powers a separate **Find the Note** mode (see a fretboard position → play it) and a centralized **History** pane with three filters (exercise kind / training type / session length). A **per-note heatmap** colors each pitch class from green (fastest) to red (slowest) based on recorded session data.
- **macOS menu bar** with real content:
  - *File*: New Session (⌘N), Stop Session (⌘.), Skip Current Note (⌘→), Reset Session History… (⇧⌘⌫, with confirmation)
  - *View*: Tuner / Trainer / Find the Note / History (⌘1–⌘4) and Toggle Listening (⌘L)
  - *Help*: Keyboard Shortcuts list (⇧⌘/), View on GitHub, Report an Issue
- **Settings scene** (⌘,) with a Sound Effects toggle and a destructive Reset Session History action.
- **About panel** with a custom credits line linking to the GitHub repository.
- **Keyboard input in Find the Note**: `A`–`G` play natural notes; `⇧A`–`⇧G` play sharps. Tooltips on each letter button show the shortcut.
- **Microphone-denied banner**: when authorization is denied or restricted, the main window shows a red banner with a one-click *Open Settings* button that deep-links to System Settings → Privacy & Security → Microphone. The banner auto-refreshes when you return to the app.
- **String/fret scope controls** in Find the Note: per-string toggles (6–1) and a fret-range picker (1–5 / 1–7 / 1–12 / 1–24) so you can drill specific neck regions.
- **Optional naming step** in Find the Note: after playing the correct pitch, the app can prompt you to also click (or type) the correct letter name to reinforce note recall.
- **Sharp/flat synonyms** in the Trainer: targets are randomly displayed as either spelling (e.g. "C#" or "Db") and either is accepted as correct.
- **Per-session standard deviation** is now recorded alongside mean time, and shown as "Avg ± σ" in result and history views.

### Changed
- Installation instructions in the README rewritten as three explicit steps with a reassuring "this is normal" framing for the Gatekeeper warning. Two-click right-click → Open is the primary path; a Terminal `xattr` one-liner is provided as a fallback.
- Trainer adopts a **400 ms post-hit refractory** so a single physical pluck cannot produce two consecutive scored hits (which was previously possible when YIN's analysis window straddled an attack transient).
- Internal architecture split: the monolithic `TrainerModel` was decomposed into `Domain`, `Exercises` (protocol + Forward/Reverse), `Sessions` (engine + persistence store), and a shared `SessionScaffold` view. No behavior change for end users.

### Fixed
- Device-picker changes in the main window are now honored mid-session because the underlying `AVAudioEngine` is recreated rather than reconfigured, which used to silently keep the previous device bound.

## [1.0.0] - 2026-05-16

First public release.

### Added
- **Tuner mode** with real-time pitch detection. Displays the closest note, frequency in Hz, and a ±50 cents tuning meter with color-graded feedback (green within ±5¢, yellow within ±15¢, red beyond).
- **Fretboard Trainer mode** with timed sessions (1 / 3 / 5 / 10 min). Random pitch-class prompts; the app waits indefinitely for the correct note. Wrong notes are silently ignored — there is no wrong-note penalty.
- **Session metrics**: live countdown with progress bar, notes hit, average time per note, and live notes-per-minute (NPM).
- **Per-duration best NPM** persisted across launches via `UserDefaults` so 1-minute and 10-minute records are tracked independently.
- **Input device selection** with hot-swap. Enumerates all input-capable Core Audio devices and switches the engine in place without restarting the app.
- **YIN-based pitch detection** with an 8192-sample sliding analysis window, parabolic interpolation, and a clarity threshold (>0.55 to register).
- **Custom app icon** featuring a stylized fretboard with a highlighted note.
- **Audio feedback** — chime on correct note, fanfare on session completion.

### Known limitations
- **Monophonic only.** Chords and overlapping notes will not be detected reliably.
- **Apple Silicon only.** No Intel build.
- **Ad-hoc signed.** Not notarized; macOS Gatekeeper requires a right-click → Open on first launch, or an `xattr` quarantine removal (see README).

[1.1.0]: https://github.com/dasaro/FretboardTrainer/releases/tag/v1.1.0
[1.0.0]: https://github.com/dasaro/FretboardTrainer/releases/tag/v1.0.0
