# RockTrigger VST3 — v0.2

RockTrigger is a small Windows VST3 audio effect that listens to incoming audio as a trigger and replaces detected hits with one of five one-shot WAV samples.

## GUI

The VSTGUI editor exposes:

- **Sound** — five-segment selector: MODERN ROCK / MODERN METAL / ALTERNATIVE / PROGRESSIVE ROCK / TRAP ROCK
- **Threshold** — -60 dB to 0 dB
- **Retrigger** — 10 ms to 500 ms, default 35 ms

Retrigger is the minimum interval allowed between two sample launches. It is useful for suppressing double-triggering from one transient, while lower values allow faster parts.

## Sample folder

The installer creates:

`Documents\RockTrigger Samples\`

Use these exact filenames:

- `modern_rock.wav`
- `modern_metal.wav`
- `alternative.wav`
- `progressive_rock.wav`
- `trap_rock.wav`

Supported WAV: mono/stereo, PCM 16/24/32-bit or float32. Files are resampled to the DAW sample rate before realtime processing.

If a selected sample is missing, RockTrigger passes the original source through instead of unexpectedly muting the track.

## No local Visual Studio build required

The repository includes `.github/workflows/build-windows.yml`.

1. Put this project in a GitHub repository.
2. Open **Actions** → **Build Windows VST3 + Installer**.
3. Press **Run workflow**.
4. Download the generated `RockTrigger-v0.2.0-Windows-x64-Setup` artifact.
5. Run the EXE installer.

The GitHub-hosted Windows runner does the MSVC/CMake build. The installer is generated with Inno Setup.

The installer uses the per-user VST3 location:

`%LOCALAPPDATA%\Programs\Common\VST3\RockTrigger.vst3`

so it does not need administrator privileges.

## Bundling your five samples into the installer

Drop the five WAV files into this repository's `samples` directory before running the GitHub Action. The Inno Setup script includes `samples\*.wav` automatically. If you prefer to keep the samples external, leave the repository sample folder without WAV files and copy them later into `Documents\RockTrigger Samples\`.

## DSP behavior

- mono → mono and stereo → stereo
- threshold detector with fast release and hysteresis
- user-adjustable 10–500 ms retrigger guard
- up to 12 overlapping one-shot voices
- dry input is replaced by the triggered sample when the selected slot is available
- VST3 32-bit and 64-bit process buffers
- state backward-compatible with v0.1 (missing Retrigger state loads at 35 ms)
