# Data format

The raw EEG recordings are personal biometric data and are not committed to this
repo. This note documents the expected format so the notebooks can be run on your
own recordings.

## Source

- **Device:** OpenBCI (4-channel EEG)
- **Sampling rate:** 200 Hz
- **Export format:** tab/comma-delimited `.txt`, with a few metadata header rows
  above the data (the loader skips them).

## Columns

The loader keeps four channels:

```
EXG Channel 0
EXG Channel 1
EXG Channel 2
EXG Channel 3
```

Other columns (sample index, accelerometer, timestamps) are ignored.

## Expected files

The notebooks expect one file per recorded condition:

| File                   | Condition                        |
|------------------------|----------------------------------|
| `CapBaseline.txt`      | Resting baseline                 |
| `CapNback.txt`         | N-back working-memory task       |
| `CapLinkedin.txt`      | Social-stress task               |
| `CapBriefrecovery.txt` | Brief recovery period            |

Place your equivalents here and adjust the filenames in the notebooks if needed.
The baseline file is required, since the neural index is computed relative to it.
