Title: fix(terminal): use native clipboard manager for terminal copy/paste on Linux

## Summary

This fixes a Linux terminal clipboard issue where `Ctrl+Shift+V` only works if
the copied text originated inside Terax itself.

## Problem

On Linux, the terminal paste path was using:

- `navigator.clipboard.readText()`

That appears to work for clipboard content Terax wrote itself, but not
reliably for clipboard content copied from external applications.

## Repro

1. Copy text inside Terax
2. Paste with `Ctrl+Shift+V`
3. Works
4. Copy text from Firefox or another app
5. Paste with `Ctrl+Shift+V`
6. Fails

## Fix

- Add `@tauri-apps/plugin-clipboard-manager`
- Use native clipboard read/write for the terminal copy/paste path
- Keep the web clipboard API as fallback
- Add focused regression tests for the fallback behavior

## Files changed

- `src/modules/terminal/lib/rendererPool.ts`
- `src/modules/terminal/lib/clipboard.ts`
- `src/modules/terminal/lib/clipboard.test.ts`
- `package.json`
- `pnpm-lock.yaml`
- `src-tauri/Cargo.toml`
- `src-tauri/Cargo.lock`
- `src-tauri/src/lib.rs`
- `src-tauri/capabilities/default.json`

## Validation

- `pnpm test src/modules/terminal/lib/clipboard.test.ts`
- `pnpm check-types`
- local production build completed successfully

## Notes

This is a terminal clipboard boundary issue, not a PTY issue. Internal copy
working while external copy fails points at the webview clipboard layer rather
than shell or terminal transport.
