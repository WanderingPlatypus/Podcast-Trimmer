# Podcast Trimmer

An interactive, text-based terminal menu for trimming episodes between a start jingle and an end jingle.

## Features

- Configure episode folder, start-jingle MP3, end-jingle MP3, and output folder.
- Independently configure the start search buffer and end search buffer in seconds (both default to 270 seconds / 4:30).
- Configure minimum match score (default `0.72`) and minimum peak margin (default `0.06`).
- Toggle `DRY RUN` / `WRITE` mode. Dry run is the default; write mode requires typing `WRITE` to confirm.
- Keeps the complete detected start jingle through the end of the detected end jingle.
- Uncertain detections are skipped. Original files are never edited, and existing outputs are never overwritten.
- Logs activity and errors to `clean_magnus_archives.log` in the episode folder.
- Uses MP3 stream copy to avoid slow re-encoding. MP3 cut points can be approximate at frame boundaries.

## Requirements

- Python 3.10 or newer.
- FFmpeg with both `ffmpeg` and `ffprobe` available on PATH.
- Python packages `numpy` and `scipy`.

## Setup and launch

1. Save `podcast_trimmer.py` in a convenient location.
2. Have your episode MP3 folder and your two jingles mp3 files ready.
3. Open a terminal in the script's folder and run:

```powershell
py podcast-trimmer.py
```

The program opens a terminal menu. Choose `1`–`9` to change settings, `R` to run, or `Q` to quit. Path options open a native operating-system picker; if you cancel, the current path is kept (the output folder can also be typed manually). No separate application window is used for the main interface, and the script does not use Tkinter.

## Menu options

| Option | Setting | Purpose |
|---|---|---|
| 1 | Episode folder | Folder containing the episode MP3s |
| 2 | Start jingle | Reference MP3 searched for near the beginning |
| 3 | End jingle | Reference MP3 searched for near the end |
| 4 | Output folder | Folder for cleaned files |
| 5 | Start search buffer | Number of seconds searched from the start |
| 6 | End search buffer | Number of seconds searched back from the end |
| 7 | Minimum match score | Required correlation score, from 0 to 1 |
| 8 | Minimum peak margin | Required difference from competing peaks |
| 9 | Mode | Toggle between `DRY RUN` and `WRITE` |

The start and end buffers are independent. A buffer of `270` means 4 minutes 30 seconds. Each buffer must be long enough to contain its entire reference jingle.

## Recommended workflow

1. Run in `DRY RUN` first and inspect `podcast_trimmer.log` for start/end positions and skipped files.
2. Listen to a few proposed boundaries. A high score is not a guarantee that the match is correct.
3. Switch to `WRITE`, choose `R`, then type `WRITE` when asked to confirm.
4. Listen to several output files, especially around the cut points.

## Safety and behavior

- Source MP3s are read-only from this tool's perspective; no source file is modified.
- Files are written only to the selected output folder. Keep it separate from the source folder for clarity.
- Existing output names are preserved; the script picks a numbered name such as `_cleaned_01` instead.
- The log is appended to, so it can contain entries from earlier runs.
- The selected settings apply for the current session only; they reset when you close the program.
- Stream-copy trimming is fast, but MP3 frames mean the cut may not be sample-exact. Check for clipped speech or a little unwanted audio at either edge.

## Troubleshooting

**FFmpeg or ffprobe is missing:** Install FFmpeg, add its `bin` directory to PATH, and restart the terminal.

**A jingle is not found:** Check that the reference file is the right jingle, and increase the corresponding search buffer if needed. Adjust thresholds cautiously; lower thresholds may accept false matches.

**A file is skipped:** Read the reason in the log. Low match scores or insufficient peak margins are intentionally treated as uncertain.

**The file picker does not open:** The script uses operating-system dialogs: PowerShell/.NET on Windows, AppleScript on macOS, or `zenity`/`kdialog` on Linux. On Linux, install `zenity` or `kdialog`. If a picker is cancelled, the current path is kept; the output folder can also be entered as a typed path.
