# Tauri App Skeleton with Android

A cross-platform desktop application built with Tauri v2, React 19, TypeScript, and Vite. Package manager is Bun.

## Prerequisites

- [Bun](https://bun.sh/) — JavaScript runtime and package manager
- [Rust](https://www.rust-lang.org/tools/install) — required for the Tauri backend
- Tauri system dependencies for your OS — see the [Tauri prerequisites guide](https://tauri.app/start/prerequisites/)

> **DevContainer users:** Everything above is pre-installed. See [DevContainer Setup](#devcontainer-setup) below.

## Quick Start

```bash
# Install dependencies
bun install

# Run the full desktop app (Rust backend + React frontend)
bun run tauri dev
```

The app window will open automatically. The frontend hot-reloads on save; Rust changes trigger a recompile.

## Common Commands

| Command | Description |
|---|---|
| `bun run tauri dev` | Run the full desktop app with hot reload |
| `bun run dev` | Run the frontend only (port 1420, no Rust) |
| `bun run build` | Type-check and build the frontend |
| `bun run tauri build` | Build a production desktop binary |

## Project Structure

```
src/              # React frontend (TypeScript)
src-tauri/        # Rust backend (Tauri)
  src/lib.rs      # Tauri commands
  tauri.conf.json # App config (window size, CSP, etc.)
  capabilities/   # Tauri permissions
public/           # Static assets
```

Frontend calls into Rust via `invoke("command_name", { args })` from `@tauri-apps/api/core`.

## DevContainer Setup

A DevContainer is provided for development on Windows without manually installing Rust or Tauri dependencies.

**Requirements:** Docker Desktop + VS Code with the Dev Containers extension.

1. Open the repo in VS Code and click **Reopen in Container** when prompted.
2. To display the app window on Windows, install and run **VcXsrv**:
   - `winget install marha.VcXsrv`
   - Launch XLaunch: Multiple windows → Start no client → check **Disable access control**
   - Allow it through Windows Firewall when prompted
3. Verify the X server connection: `xdpyinfo` (should print display info)
4. Run `bun run tauri dev` — the window will appear on your Windows desktop.

## IDE Setup

- [VS Code](https://code.visualstudio.com/) with the [Tauri extension](https://marketplace.visualstudio.com/items?itemName=tauri-apps.tauri-vscode) and [rust-analyzer](https://marketplace.visualstudio.com/items?itemName=rust-lang.rust-analyzer)