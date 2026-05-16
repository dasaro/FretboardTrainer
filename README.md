<div align="center">

<img src="assets/icon-128.png" alt="FretboardTrainer icon" width="128" height="128" />

# Fretboard Trainer

**A chromatic tuner and ear-training game for macOS.**

Plug in your guitar (or any monophonic instrument), and either tune up or run timed drills where the app randomly throws notes at you and times how fast you can play them.

[![Latest release](https://img.shields.io/github/v/release/dasaro/FretboardTrainer?label=download&style=flat-square)](https://github.com/dasaro/FretboardTrainer/releases/latest)
[![macOS](https://img.shields.io/badge/macOS-14%2B-blue?style=flat-square)](https://www.apple.com/macos/)
[![License: MIT](https://img.shields.io/badge/license-MIT-green?style=flat-square)](LICENSE)

</div>

---

## Features

- **Tuner mode** — real-time pitch detection with cents-accurate tuning meter (50–2000 Hz range, ±1¢ resolution on a clean signal).
- **Fretboard Trainer mode** — random pitch-class drills with selectable session lengths (1 / 3 / 5 / 10 min). The app waits for you to play the correct note, then advances. Wrong notes are simply ignored — no penalty, just play until you nail it.
- **Performance metrics** — live countdown, notes hit, average time per note, and notes-per-minute (NPM). Best NPM per session length is saved across launches.
- **Input device picker** — selectable input device with hot-swap. Works with built-in microphones, USB audio interfaces, and aggregate devices.
- **YIN-based pitch detection** — 8192-sample analysis window with parabolic interpolation and clarity-based confidence gating. Monophonic only.

## Requirements

- macOS 14 (Sonoma) or later
- Apple Silicon (arm64)
- A microphone or audio input device

## Installation

1. Download the latest release zip from the [Releases page](https://github.com/dasaro/FretboardTrainer/releases/latest).
2. Unzip it — you'll get `FretboardTrainer.app`.
3. Drag `FretboardTrainer.app` to your `/Applications` folder.
4. **First launch (Gatekeeper)**: this app is ad-hoc signed, not notarized by Apple. On first launch macOS will refuse to open it with an "unidentified developer" warning. To bypass:
   - **Easy way**: right-click (or Control-click) `FretboardTrainer.app` in Finder → choose **Open** → click **Open** in the dialog. macOS remembers this choice for future launches.
   - **Alternative**: open Terminal and run:
     ```sh
     xattr -d com.apple.quarantine /Applications/FretboardTrainer.app
     ```
5. The first time the app runs, macOS will ask for microphone access. Grant it.

## Usage

### Tuner mode

1. Click **Start Listening**.
2. Pick your input device from the dropdown if needed.
3. Play a note. The detected pitch, frequency in Hz, and a ±50 cents tuning meter appear. The meter dot is **green** within ±5¢, **yellow** within ±15¢, and **red** beyond.

### Fretboard Trainer mode

1. Switch the mode picker to **Fretboard Trainer**.
2. Click **Start Listening** if you haven't already.
3. Pick a session length: **1**, **3**, **5**, or **10 min**.
4. Click **Start Session**.
5. A random pitch class (C, C#, …, B) appears. Play it on any string in any octave — when the app hears it, you advance to the next.
6. After the timer runs out, you'll see your **notes per minute**, total notes, and average time. If you beat your previous best for that session length, a **NEW BEST** badge appears.

> Tip: the app gives you the benefit of the doubt. If you fumble or hit a wrong note while reaching for the target, it's silently ignored. Only the moment you play the correct pitch class counts.

### Keyboard shortcuts

| Key       | Action                       |
|-----------|------------------------------|
| `Space`   | Start / Stop Listening       |

## Troubleshooting

**No input devices in the dropdown** — click the refresh button next to the picker. If your interface still doesn't appear, check **System Settings → Privacy & Security → Microphone** and ensure FretboardTrainer is allowed.

**Pitch detection is unreliable** — the detector is monophonic. Single notes work cleanly; chords, palm-muted notes, and very low/quiet notes are harder. Make sure your signal level fills the meter at least halfway when you play.

**App won't launch** — re-run the `xattr` command above, or right-click → Open from Finder. If you've already moved the app and granted Gatekeeper approval, normal double-click should work.

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for the full release history.

## License

[MIT](LICENSE) © 2026 dasaro
