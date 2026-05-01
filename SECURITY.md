# Security policy

## Reporting a vulnerability

If you find a security issue in this project, please **do not** open a public
GitHub issue.

Instead, use GitHub's private vulnerability reporting:

1. Go to the [Security tab](https://github.com/ahkarhul/ascii-video-converter/security)
2. Click **Report a vulnerability**
3. Describe the issue with enough detail to reproduce

Reports are reviewed on a best-effort basis. This is a small personal project
so I cannot promise an SLA, but I will respond when I can.

## Scope

This is a static, browser-based tool. In scope:

- XSS or other injection paths in the rendered ASCII output
- Issues in the Python helper server (`http.server.py`) when bound to a
  non-loopback interface
- Supply-chain risk in build/CI workflows

Out of scope:

- Vulnerabilities in `ffmpeg.wasm`, `mp4-muxer`, `gif.js`, or other upstream
  dependencies — please report those upstream.
