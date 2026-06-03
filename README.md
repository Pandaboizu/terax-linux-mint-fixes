# terax-linux-mint-fixes

Artifacts for upstreaming a Linux clipboard paste fix to Terax.

## Contents

- `patches/terax-linux-external-clipboard-fix.patch`
  Git patch against `crynta/terax-ai` that switches terminal copy/paste from
  the web clipboard API to Tauri's native clipboard manager, with fallback.

- `drafts/issue.md`
  Ready-to-paste GitHub issue describing the bug and root cause.

- `drafts/pr.md`
  Ready-to-paste pull request description for the upstream Terax repo.

## Problem

On Linux, Terax terminal paste via `Ctrl+Shift+V` works when the copied text
came from inside Terax, but fails when the copied text came from another app.

## Fix summary

- Add `@tauri-apps/plugin-clipboard-manager`
- Add native clipboard read/write for terminal paste/copy
- Keep web clipboard fallback
- Add regression tests for clipboard fallback behavior

## Validation already run

- Built patched Terax binary locally
- `vitest run src/modules/terminal/lib/clipboard.test.ts`
- `tsc --noEmit`
