# ASCII Video Converter

Browser-based tool that turns any video into an ASCII animation and exports it
as MP4, GIF, a text frame, or a self-playing terminal shell script. Decoding is
handled by `ffmpeg.wasm` by default, with an opt-in browser-decoder path for
short clips.

## Live demo (GitHub Pages)

The app is served as a static demo at:

> **https://ahkarhul.github.io/ascii-video-converter/**

**Note:** GitHub Pages cannot set `Cross-Origin-Opener-Policy` /
`Cross-Origin-Embedder-Policy` headers, so `SharedArrayBuffer` is unavailable
there and the app falls back to the **single-threaded** `ffmpeg.wasm` core.
Everything still works — decoding and all export formats — just without the
~2–4× multi-threaded speed-up. For full performance use the local server
described below.

## Files

- `ascii-video-converter-v2.html` — the full app (UI, ASCII renderer,
  ffmpeg.wasm integration, WebCodecs encoder, GIF/MP4/shell exporters).
  Everything lives here.
- `http.server.py` — a tiny static file server that adds the COOP/COEP headers
  required to enable `SharedArrayBuffer` in the browser.

## Running

```sh
python3 http.server.py
```

Then open http://localhost:8080/ascii-video-converter-v2.html in a browser.

## Why the custom server?

`ffmpeg.wasm` has a multi-threaded core that is ~2–4× faster than the
single-threaded build on multi-core machines. It relies on `SharedArrayBuffer`,
which browsers only expose to pages served with these two response headers:

```
Cross-Origin-Opener-Policy: same-origin
Cross-Origin-Embedder-Policy: require-corp
```

`python3 -m http.server` does *not* send these, so the app would silently fall
back to the single-threaded ffmpeg core. `http.server.py` is the minimal
`SimpleHTTPRequestHandler` subclass that injects the two headers on every
response, unlocking the MT build and cutting decode/encode times significantly.

You can confirm it's working by opening DevTools and checking
`window.crossOriginIsolated` — it should be `true`. The in-app log will also
report `Using multi-threaded ffmpeg core.`

## Requirements

- Python 3 (standard library only — no pip packages needed).
- A modern browser. Chromium-based browsers get the fastest path thanks to
  WebCodecs H.264 encoding; Firefox works but automatically routes MP4 export
  through `ffmpeg.wasm` (libx264) since it ships decode-only WebCodecs.
