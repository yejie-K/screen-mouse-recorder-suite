# Security policy

## Reporting a vulnerability

Do not publish exploitable details in a public issue. Contact the repository owner
privately with a description, reproduction steps, affected version, and suggested
mitigation. Allow reasonable time for validation and remediation before disclosure.

## Deployment boundaries

- The web workbench is intended for local loopback use.
- Do not expose its HTTP server directly to a public or untrusted network.
- Treat recordings, OCR crops, mouse logs, and generated reports as sensitive local data.
- Review configuration files before sharing them because they may contain absolute paths.
- Obtain FFmpeg, OCR engines, and other dependencies from trusted sources.

The project is provided as a source snapshot. Users are responsible for local access
control, backups, dependency updates, and compliance with applicable privacy rules.
