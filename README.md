# SIGNALIQ

Automated IQ and WAV signal analysis and parameter extraction, running entirely in the browser.
Prototype for SIH26147 (NTRO).

Files never leave your machine: everything is decoded and analysed locally in JavaScript.

## Run it

Open `index.html` in any modern browser. No build step, no dependencies, no server needed.
(Fonts load from Google Fonts when online and fall back to system fonts offline.)

### Host it on GitHub Pages

1. Push this folder to a GitHub repository.
2. Go to **Settings > Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**, select `main` and `/ (root)`, then save.
4. Your site appears at `https://<your-username>.github.io/<repo-name>/`.

## Supported inputs

| Type | Formats |
|---|---|
| WAV | PCM 8/16/24/32-bit, IEEE float 32/64-bit, any channel count (left, right, mix, or stereo as I/Q) |
| Raw IQ | Complex float32 (`.cf32`, `.cfile`), int16 (`.cs16`), int8 (`.cs8`), uint8 (`.cu8`, RTL-SDR), float64 (`.cf64`) |

Raw IQ files carry no sample rate, so set it in the sidebar. It is auto-filled when the filename
follows the pattern `name_<centreHz>_<sampleRateHz>_fc.ext` (for example `capture_433920000_1000000_fc.cf32`).
The first 4,194,304 samples of a file are analysed.

## Pipeline

1. Validate: file type, readability, metadata
2. Decode: samples into a standard array
3. Preprocess: DC removal, optional normalisation
4. Time domain: peak, RMS, crest factor, IQ balance, burst detection, instantaneous frequency
5. Frequency domain: windowed, averaged FFT and spectrogram
6. Parameters: spectral peaks, 99% and 3 dB bandwidth, centroid, SNR estimate, signal-type hint
7. Visualise: waveform, spectrum, spectrogram, constellation
8. Report: structured JSON (all values in SI units)

SNR, bandwidth and the signal-type hint are estimates from the averaged spectrum, not calibrated measurements.

## Test files

The `samples/` folder holds three signals to try:

| File | Signal |
|---|---|
| `qpsk_burst_433920000_1000000_fc.cf32` | QPSK burst, 1 MS/s, 433.92 MHz, with a 150 kHz spur |
| `fm_tone_100100000_250000_fc.cu8` | FM carrier at +20 kHz, 1 kHz tone, 5 kHz deviation, 250 kS/s |
| `audio_tones.wav` | 440 Hz and 1,320 Hz tones plus a sweep, 44.1 kHz |

The page also has four built-in synthetic samples in the sidebar.

## Project layout

```
index.html   the whole application (HTML, CSS and JavaScript in one file)
samples/     test signals
README.md
```

## Tuning

`MIN_STAGE` near the top of the script sets the minimum time each pipeline step stays visible (milliseconds).
Set it to `0` to remove the artificial delay.
