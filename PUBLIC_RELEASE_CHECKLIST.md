# Public release checklist

Complete these checks immediately before creating a public commit or pushing to GitHub.

## Repository contents

- [ ] No `sessions/`, `outputs/`, `experiments/`, `analysis_output/`, or local archives.
- [ ] No videos, mouse logs, OCR crops, screenshots, reports, or real-session metadata.
- [ ] No `config.json`, `.env` files, credentials, tokens, cookies, or private keys.
- [ ] No user names, home-directory paths, drive-specific working paths, or device IDs.
- [ ] No internal agent instructions, development transcripts, or private research notes.
- [ ] Included third-party assets and dependencies have compatible licenses.

## Product claims

- [ ] ScreenRecorder is described as the stable recording entry point.
- [ ] JourneyAnalyzer remains clearly marked Beta.
- [ ] Automated OCR or AI output is described as a candidate requiring human review.
- [ ] Draft previews are not presented as confirmed research conclusions.

## Verification

- [ ] Run the full test suite on a clean Python 3.10+ environment.
- [ ] Run a secret scanner and an absolute-path scan over tracked files.
- [ ] Inspect the staged diff with `git diff --cached`.
- [ ] Confirm the target repository, visibility, branch, and license before pushing.
- [ ] If an older public history contains sensitive data, rewrite or replace that history
      before treating deletion from the latest commit as sufficient.
