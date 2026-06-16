# EEG Stress Sonification Pipeline

An end-to-end Python pipeline that turns raw multi-channel EEG into a continuous
neural index and maps it to sound. Built for my undergraduate capstone, which
explores how the physiological signature of stress can be made audible.

This repo is the **signal-processing and feature-extraction** half of that
project: acquiring raw EEG, cleaning it, extracting band-power features, and
analyzing how they shift across cognitive conditions.

## The problem

Raw EEG off consumer hardware is messy: noisy channels, artifacts, and
variable-length recordings. The goal here is to take that raw signal and pull
out a stable, interpretable feature — alpha-band power — that tracks cognitive
load over time, then render it as a continuous control signal.

## Data

Recordings were captured on an **OpenBCI** board: **4 EEG channels, 200 Hz**
sampling rate, exported as tab-delimited `.txt` files. Four conditions were
recorded (baseline, an n-back working-memory task, a social-stress task, and a
brief recovery period).

The raw recordings are personal biometric data and are **not included** in this
repo. See [`data/README.md`](data/README.md) for the exact file format so the
pipeline can be run on your own recordings.

## Method

1. **Load & clean** — parse OpenBCI exports, keep the 4 EXG channels, coerce to
   numeric, and drop invalid rows.
2. **Bandpass filter** — 4th-order Butterworth filter isolating the alpha band
   (8–13 Hz), applied with zero-phase `filtfilt`.
3. **Band-power extraction** — sliding-window log power (0.5 s window, 0.1 s
   step), averaged across channels.
4. **Neural index** — alpha suppression relative to the baseline condition,
   smoothed with a moving average and normalized to a 0–1 range per segment.
5. **Spectral analysis** — short-time Fourier transform (STFT) spectrograms per
   condition, rendered as animated polar heatmaps to visualize how the frequency
   content shifts over time.
6. **Sonification** — the normalized index is mapped to MIDI control-change
   values and exported as `.mid` files for use in a score.

## Outputs

- **MIDI** — continuous control signals exported per condition
  (`<condition>_stress.mid`), plus a jazz-scale pitch mapping variant.
- **Spectrogram heatmaps** — animated STFT visualizations per condition.

(Add a couple of example `.mid` files and a heatmap GIF to `outputs/` so they
render directly on GitHub.)

## Repo structure

```
.
├── notebooks/
│   ├── 01_signal_processing.ipynb   # filtering, band-power, index, MIDI export
│   └── 02_spectrogram_heatmap.ipynb # STFT spectrograms + animated heatmaps
├── data/
│   └── README.md                    # expected file format (raw data not included)
├── outputs/                         # example MIDI / heatmap exports
├── requirements.txt
└── .gitignore
```

## Running it

```bash
pip install -r requirements.txt
```

Place your own OpenBCI recordings in `data/` (see `data/README.md` for the
format), then run the notebooks in order.

## Stack

Python · NumPy · SciPy (`signal.butter`, `filtfilt`, `spectrogram`) · pandas ·
Matplotlib · mido

## Notes

This is the front half of a sleep/biosignal-staging-style pipeline — real-world
acquisition, artifact handling, band-power and spectral feature extraction. The
capstone uses it for stress sonification; the same feature-extraction approach
generalizes to other EEG classification and staging problems.
