Title: Ctrl+Shift+V fails for text copied outside Terax on Linux

## Summary

On Linux, terminal paste with `Ctrl+Shift+V` works for text copied inside Terax, but fails for text copied from other apps.

## Environment

- Terax version: `0.7.3`
- Platform: Linux Mint
- App type: bundled Tauri desktop app

## Repro

1. Open Terax.
2. Copy some text inside Terax.
3. Paste into the Terax terminal with `Ctrl+Shift+V`.
4. Observe: paste works.
5. Copy some text from an external app, for example Firefox, another terminal, or a text editor.
6. Paste into the Terax terminal with `Ctrl+Shift+V`.
7. Observe: paste does not work.

## Expected

`Ctrl+Shift+V` should paste the current system clipboard into the terminal regardless of where the text was copied from.

## Actual

Paste works only when the clipboard content originated inside Terax itself.

## Root cause

The terminal paste path uses the webview clipboard API:

- `navigator.clipboard.readText()`

On Linux/WebKit this appears able to read clipboard content written by Terax itself, but not reliably read clipboard content copied from external applications.

## Confirmed fix

Switch terminal copy/paste to Tauri's native clipboard plugin instead of relying on the web clipboard API for the terminal path:

- use `@tauri-apps/plugin-clipboard-manager` `readText()` for paste
- use `@tauri-apps/plugin-clipboard-manager` `writeText()` for copy
- keep web clipboard as fallback only

This fixes the problem locally.

## Suggested implementation area

- `src/modules/terminal/lib/rendererPool.ts`

## Notes

This is specifically about terminal paste behavior. It is not a PTY issue. Internal copy/paste behavior strongly suggests the failure is at the clipboard boundary between WebKit and the OS clipboard.
