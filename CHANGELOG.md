# Changelog

All notable changes to FretboardTrainer are documented here. The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.5.0] - 2026-06-13

Two new modes — a metronome that listens to your timing, and a string-bending trainer — plus a structural change to how every drill is scoped and tracked.

### Added
- **Metronome.** A precise, in-app-synthesized click (beep or drum, with optional time signatures and an accented downbeat). Its experimental **"Detect my tempo"** layer listens to a USB/DI instrument as you play along and reports three things: your **tempo** (a robust live BPM from your plucks), your **timing** (early/late against the click, after a one-time calibration that measures and subtracts the detection latency), and your **consistency** (the spread of your beat intervals). Onsets are timestamped sub-frame off the audio clock, so the live BPM moves continuously instead of snapping to a few values. Every run of 8+ detected beats is recorded to History.
- **Bending Trainer.** A timed drill (1 / 3 / 5 / 10 min) for bend intonation: the app names a note and a bend width — +1 or +2 semitones commonly, +3 occasionally, +4 rarely — and you find that note anywhere, play it, and bend up to the target. A live cents meter tracks the bend; the note you actually *reach and hold* is scored 0–100 on cents accuracy, so an overshoot-then-settle is judged at the settle. Optionally hear the two notes first — the classic way bends are taught. Per-drill and per-bend-width results are recorded to History.
- **Practice zones — strings and fret sections.** Restrict any fretboard drill to a chosen set of strings and neck sections (0–7, 7–12, 12–19, 19–24), combinable. On the position-generating drills (Find the Note, Note on String, Open Strings) the restriction is enforced; on the pitch-class drills (Trainer, Intervals, Scale Degrees, Chord Builder) it is honor-system. Either way the zone is the label your History and adaptive difficulty partition by — so "4th & 5th · frets 7–12" builds its own profile. Persisted per exercise. This replaces the old freeform "training type" text field.
- **History covers every mode.** One Mode picker now spans the note exercises plus **Tempo** (metronome detection runs) and **Bending** drills, each with its own Progress chart and table; Bending also gets a per-bend-width Mastery breakdown. The Adaptive-vs-Uniform distinction extends to per-item Mastery, with an All / Adaptive / Normal filter on both Progress and Mastery. The daily-practice total counts bending and metronome time too, not just note-exercise sessions.

### Changed
- **Find the Note always asks you to name the note** — the naming step is no longer optional, and the "Heard: X" readout is hidden in this mode (it would spell out the answer).
- **The input-level meter is consistent** — shown whenever you are listening, in every mic-using mode.
- **"Reset all practice history" now clears adaptive (SM-2) state too**, so a fresh start no longer keeps biasing toward your old weak notes. The reset also clears the new tempo and bending logs.
- A single, consistent "Start Listening" prompt across modes.

### Fixed
- **Input could silently stop in some modes.** Starting a second audio engine — the metronome click, or the bending note preview — reconfigured the shared audio device and stopped the input tap while the app still showed "Listening", so notes quietly stopped registering. The input engine now rebuilds on an audio-configuration change, so listening survives the metronome/preview starting and audio-route changes (headphones, interfaces). The Tuner was unaffected because it never starts a second engine.
- Numerous tempo-detection and bending data/GUI fixes from internal review: sub-frame onset precision for soft attacks, early-stop drill summaries, settings-persistence clobbers on launch, and audio-thread work gated to when it is actually needed.

## [1.4.0] - 2026-06-07

A big release: History is rebuilt around per-item mastery, two new exercises join the lineup, and an experimental polyphonic chord detector lands.

### Added
- **History, rebuilt as Progress + Mastery.** Every scored hit is now recorded as an attempt tagged with *the thing the drill actually trains* (the interval, chord tone, scale degree, string, or circle relationship), not just the resulting pitch class. Two views sit on top: **Progress** charts your trend over time (notes-per-minute and four other metrics, Date or Session #, Adaptive vs Uniform distinguished), and **Mastery** is a weakest-first per-item table — recent average time, miss rate, attempt count, and an improving/worsening trend arrow — so you can answer both "am I improving?" and "what am I weakest at right now?". Mastery data accrues from this version onward; the Progress chart keeps your full session history.
- **Chord Builder exercise.** Find a seed note on a string, then construct a triad or 7th chord *around* it, playing the remaining tones anywhere on the neck. Toggles: find-on-a-string first (with open-string verification), let the anchor be any chord tone — root, 3rd, 5th, 7th — so you work out the root yourself, and name the notes at the end (inline after each tone, or all together in play order). Chord set selectable (Triads / Triads + 7th / Custom).
- **Circle of 5ths exercise.** Active-recall practice of the circle — the most evidence-backed way to learn it. A key is shown and you retrieve and *play* the next position from memory: a fifth up (clockwise), a fourth up (counter-clockwise), or the relative minor. Random order by default (maximum retrieval demand); an optional ordered cycle (C → G → D → …) drills the sequence for muscle memory. Keys are spelled conventionally, with flats on the flat side (Bb, Eb, Ab, Db, Gb). SM-2 spacing surfaces the keys/relationships you recall slowest.
- **Chord ID (experimental).** A live, read-only readout — in the **View** menu, not the main picker — that names the major/minor triad you're holding, via frequency-domain chroma analysis (FFT → 12-bin chroma → triad-template cost) with harmonic suppression. Independent of the monophonic detector the exercises use. A confidence and "3rd-strength" meter flags the classic single-note-overtone false positive.
- **Enharmonic distinction in Mastery (Trainer).** The Trainer is the only mode that shows both spellings of a pitch class and asks you to find the one displayed, so its Mastery rows now separate "Eb" from "D#" — revealing which spelling is cognitively slower for you. Pitch matching and adaptive selection are unaffected.

### Changed
- **Wrong-note detection is now uniform across every mode.** Detection lives in a single, exercise-agnostic path driven only by the exercise protocol. A subtle divergence — where multi-step modes (open-string verification, the root before an interval, the chord anchor) could miscount a *correct* note's ring-out as wrong — was fixed by registering every correct match for decay suppression, scoring or not. All modes now behave identically.

## [1.3.0] - 2026-06-03

A release focused on a new speed drill and much smarter wrong-note handling.

### Added
- **Open Strings exercise.** A pure string-skipping speed drill: one open string (E A D G B E) is named at a time and you race to pluck it. Matching is octave-aware, so a "high E" prompt (E4, 1st string) isn't satisfied by ringing the low E (E2, 6th) — implemented via an optional per-exercise octave constraint with ±1 tolerance to absorb single-octave detector wobble. Adaptive selection (keyed on string number) and full history tracking work like the other modes. Switch to it with ⌘7.
- **Per-session wrong-note counting.** Each session now tallies wrong notes and surfaces the count live during the run and on the result card. A matching **"Wrong notes per session"** Y-axis was added to the History chart.
- **"Show detected note" with a distance gradient.** With the Settings → Practice toggle on, the live "Heard: X" readout is tinted on a green→red gradient by the circular semitone distance from the target, giving instant feedback on *how* far off a played note is rather than just whether it matched.
- **History distinguishes Adaptive vs Uniform sessions.** Each session records whether adaptive selection was active, and the chart encodes the two modes by both colour and shape (with a legend), so their not-directly-comparable NPM numbers are never conflated. The stats line also breaks down the split when both are present. Pre-existing records default to Uniform.

### Changed
- **Wrong-note detection is far more robust.** Wrong notes are only counted after a fresh envelope-based onset, so sustained ring-out and low-level noise (fan hum, mic bumps, decaying strings) no longer register. The decay of your *previous* correct note is additionally suppressed for ~1.5 s so its tail isn't charged as a new mistake. Octave mismatches in Open Strings are a gentle no-op rather than a logged error.
- **Intervals — clearer two-step cue.** In "play root, then the interval" mode, landing the root now plays a soft confirmation and flashes the root green so you know it's time to play the target.

## [1.2.0] - 2026-05-18

A focused release on practice quality and history visualization.

### Added
- **Adaptive note selection (opt-in)** — Settings → Practice → "Adaptive note selection" toggle. When enabled, the trainer biases its random selection toward notes you're slower on, using the SM-2 spaced-repetition algorithm. Each pitch class carries an Easiness Factor that updates after every hit; weaker notes (low EF) appear up to ~3.7× more often than mastered ones. Forward and reverse exercises maintain separate EF maps so the two skills don't bleed into each other. Off by default; uniform random when disabled.
- **History chart X / Y axis selectors.** The History pane now lets you plot any of four Y metrics — **Notes per minute**, **Mean time per note (s)**, **Notes per session**, or **Note timing σ (s)** (consistency) — against either **Date** (chronological) or **Session #** (equally-spaced, collapses time gaps). Useful for asking different questions of the same data: "Am I getting faster?" vs. "Am I getting more consistent?" vs. "Did I take a week off?"

### Changed
- **History pane visual refresh.** Refactored the controls block into two aligned rows (data filters + chart axes) using a shared small-caps section label pattern that matches the existing heatmap header. Pickers now share a single `CompactPicker` view modifier so spacing and sizing are consistent across the pane. Continuous corner radii on the card and heatmap cells (the macOS "squircle"), session-count rendered as a Capsule badge.
- **Heatmap colors softened** — saturation/opacity lowered so the green→red gradient reads as a tint rather than a web-style heatmap, and stroke width tightened from 1pt to 0.5pt.
- **Training-type field cleaned up.** Removed the double-chevron visual glitch (two stacked indicators) and replaced the icon-only popup with a labeled `Recent` menu using an inline `Picker`, so macOS draws its native checkmark next to the currently-selected entry. Placeholder text now shows an example.
- **Fretboard orientation flipped** to match standard tablature notation — high E on top, low E on bottom.

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

[1.4.0]: https://github.com/dasaro/FretboardTrainer/releases/tag/v1.4.0
[1.3.0]: https://github.com/dasaro/FretboardTrainer/releases/tag/v1.3.0
[1.2.0]: https://github.com/dasaro/FretboardTrainer/releases/tag/v1.2.0
[1.1.0]: https://github.com/dasaro/FretboardTrainer/releases/tag/v1.1.0
[1.0.0]: https://github.com/dasaro/FretboardTrainer/releases/tag/v1.0.0
