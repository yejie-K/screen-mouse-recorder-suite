# Privacy and local data handling

Screen Mouse Recorder is designed to run locally. It can capture selected screen pixels
and structured mouse activity, which may contain personal, confidential, or copyrighted
information depending on what is visible during recording.

## Data captured by the recorder

- Video pixels inside the selected recording region.
- Mouse positions, clicks, wheel actions, and drag events.
- Session timing, recording-region metadata, and locally generated summaries.

Keyboard input and audio are not recorded by the current implementation.

## Files that must not be committed

Never commit real recordings, mouse logs, session metadata, OCR crops, screenshots,
analysis workspaces, exported reports, local configuration, or machine-specific paths.
The repository `.gitignore` excludes the standard locations and file types, including:

```text
config.json
sessions/
outputs/
experiments/
analysis_output/
local_archive/
*.mp4
*.jsonl
*.log
```

Before sharing a report or screenshot, review it manually for account names, chat
messages, notifications, file paths, device identifiers, game account information, and
third-party copyrighted material.

## Storage and network behavior

Session data is stored on the local machine. The recording and deterministic analysis
pipeline does not require uploading session content to a cloud service. If an operator
chooses to use an external AI or OCR service, they are responsible for obtaining consent
and reviewing that service's data-retention terms before sending any content.
