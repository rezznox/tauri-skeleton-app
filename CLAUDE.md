# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Tauri Skeleton is a cross-platform desktop application built with **Tauri v2** (Rust backend) + **React 19** + **TypeScript 5.8** + **Vite 7**. Package manager is **Bun**.

## Common Commands

```bash
# Frontend dev server only (port 1420)
bun run dev

# Full desktop app development (Rust + frontend)
bun run tauri dev

# Production build (TypeScript check + Vite build + Tauri bundle)
bun run tauri build

# Type-check + build frontend only
bun run build

# Install dependencies
bun install
```

No test framework or linter is currently configured.

## Architecture

### Frontend (`src/`)
React SPA using functional components with hooks. Entry point is `main.tsx` → `App.tsx`. Plain CSS for styling with dark mode via `prefers-color-scheme`.

### Backend (`src-tauri/src/`)
Rust crate (`tauri_skeleton_2026_lib`). Tauri commands are defined with `#[tauri::command]` in `lib.rs` and registered in the `run()` function's `invoke_handler`. `main.rs` is a minimal entry point that calls `run()`.

### Frontend ↔ Backend Communication
Frontend calls Rust commands via `invoke("command_name", { args })` from `@tauri-apps/api/core`. Commands are async on the frontend side.

### Tauri Configuration
- `src-tauri/tauri.conf.json` — app config, window settings (800×600), CSP, build commands
- `src-tauri/capabilities/default.json` — permissions (core:default, opener:default)

### TypeScript
Strict mode enabled with `noUncheckedIndexedAccess`, `noImplicitOverride`, `noFallthroughCasesInSwitch`. Bundler module resolution. No emit (Vite handles bundling).

## Dev Environment

A DevContainer is provided (`.devcontainer/`) with Ubuntu 22.04, Node 22, Rust, Bun, and Tauri system dependencies pre-installed.

### X Server Setup (Windows Host)

Tauri's GTK backend requires an X11 display server. When developing inside the DevContainer on Windows, you need an X server running on the host:

1. **Install VcXsrv** — `winget install marha.VcXsrv` or download from [SourceForge](https://sourceforge.net/projects/vcxsrv/)
2. **Launch XLaunch** with these settings:
   - Multiple windows
   - Start no client
   - Check **Disable access control** (allows the container to connect without xauth tokens)
3. **Allow through Windows Firewall** — VcXsrv will prompt on first launch; allow on private networks
4. **Rebuild the DevContainer** if this is the first time adding X11 support

The DevContainer is already configured with `DISPLAY=host.docker.internal:0`. To verify the connection works, run `xdpyinfo` inside the container — it should print display info without errors. Then `bun run tauri dev` should launch the app window on your Windows desktop via VcXsrv.

### Android Development

The DevContainer includes the Android SDK (platform-tools, platforms;android-35, build-tools;35.0.1), NDK 27.2, OpenJDK 17, and Rust Android cross-compilation targets (`aarch64-linux-android`, `armv7-linux-androideabi`, `i686-linux-android`, `x86_64-linux-android`). Environment variables `ANDROID_HOME`, `NDK_HOME`, and `JAVA_HOME` are pre-configured.

The Android SDK is stored on a named Docker volume (`Tauri-Skeleton-2026-android-sdk`) so it persists across container rebuilds.
