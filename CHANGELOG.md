# Changelog

All notable changes to FretboardTrainer are documented here. The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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

[1.0.0]: https://github.com/dasaro/FretboardTrainer/releases/tag/v1.0.0
