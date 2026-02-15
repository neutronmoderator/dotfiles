# Mac Restore Tool -- Scope

A single script (`restore.sh`) that takes a fresh macOS install and reproduces this exact setup. Idempotent -- safe to re-run.

## What it covers

### 1. Package managers
- **Homebrew**: install if missing, then `brew bundle` from a `Brewfile`
  - 167 formulas, 38 casks (auto-generated with `brew bundle dump`)
- **uv tools**: `domain-search`, `git-filter-repo`, `nano-pdf`, `pi-fzf`, `pif-cli`
- **Bun global packages**: everything in `~/.bun/install/global/node_modules/`

### 2. Shell (fish)
- Install fish via Homebrew (in Brewfile)
- Set as default shell (`chsh -s /opt/homebrew/bin/fish`)
- Copy `sources/config.fish` to `~/.config/fish/config.fish`
- Declare shell aliases (port from `shell.nix` to fish functions or `conf.d/`)
- Install fish plugins if any (currently none via fisher)

### 3. Dotfiles / configs (`~/.config/`)
Files to symlink or copy from this repo:

| App | Source | Destination |
|-----|--------|-------------|
| Ghostty | `sources/ghostty.conf` | `~/.config/ghostty/config` |
| Zed settings | `sources/zed-settings.json` | `~/.config/zed/settings.json` |
| Zed keymap | `sources/zed-keymap.json` | `~/.config/zed/keymap.json` |
| Git | `sources/gitconfig` | `~/.gitconfig` |
| 1Password SSH | `sources/1password-ssh-agent.toml` | `~/.config/1Password/ssh/agent.toml` |
| Karabiner | `~/.config/karabiner/` | snapshot + restore |
| yabai | `~/.config/yabai/` | snapshot + restore |
| skhd | `~/.config/skhd/` | snapshot + restore |
| borders | `~/.config/borders/` | snapshot + restore |
| btop | `~/.config/btop/` | snapshot + restore |
| nvim | `~/.config/nvim/` | snapshot + restore |
| linearmouse | `~/.config/linearmouse/` | snapshot + restore |
| Raycast | `~/.config/raycast/` | snapshot + export/import |
| zellij | `~/.config/zellij/` | snapshot + restore |

### 4. macOS system preferences (`defaults write`)
Snapshot and restore key domains:
- `com.apple.dock` (autohide, size, hot corners, etc.)
- `com.apple.finder` (show extensions, default view, etc.)
- `com.apple.Safari` (if used)
- `NSGlobalDomain` (key repeat rate, scroll direction, etc.)
- `com.apple.screencapture` (location, format)
- `-g` AppleShowAllExtensions, etc.

### 5. Login items / launch agents
- Login items: AlDente, Raycast, LinearMouse, Thaw, Vivid, FineTune, boringNotch, CCUsage, DockDoor
- User LaunchAgents (copy plists):
  - `com.agentsc.email-sync.plist`
  - `com.agentsc.pi-daily.plist`
  - `com.sasha.x-sync.plist`
  - `sh.ntfy.typefully.plist`

### 6. SSH + GPG signing
- 1Password SSH agent config (already in sources)
- Git signing via `~/.ssh/id_ed25519` (local key)
- SSH config (`~/.ssh/config`)

### 7. App-specific state (best-effort)
- Raycast: export/import settings + extensions
- Karabiner: complex modifications JSON

### 8. pi agent setup
- `~/.pi/` directory structure
- Skills, extensions, config

---

## Design

```
dotfiles/
├── mac-restore/
│   ├── restore.sh          # Main entry point
│   ├── Brewfile             # Auto-generated, checked in
│   ├── defaults.sh          # macOS defaults write commands
│   ├── snapshot.sh          # Captures current state into this dir
│   └── configs/             # Snapshotted ~/.config/* dirs
├── sources/                 # Existing config files (shared with NixOS)
└── ...
```

**Two commands:**
- `snapshot.sh` -- run on current Mac to capture everything into the repo
- `restore.sh` -- run on a fresh Mac to set everything up

### Principles
- Idempotent: every step checks before acting
- No secrets in repo: API keys stay in 1Password, restore script uses `op` CLI to inject
- Symlinks where possible (configs point back to repo)
- Brewfile is the single source of truth for packages
- `defaults` commands are explicit, not a blind plist dump

---

## Order of operations (restore.sh)

1. Install Xcode CLI tools
2. Install Homebrew
3. `brew bundle install`
4. Set fish as default shell
5. Symlink dotfiles (git, ghostty, zed, 1password, karabiner, etc.)
6. Apply macOS defaults
7. Install uv tools
8. Install bun globals
9. Copy LaunchAgents
10. Print manual steps (login items, App Store apps, 1Password setup)

---

## Out of scope (for now)
- Full disk backup / Time Machine
- iCloud state
- Browser bookmarks/extensions (managed separately)
- App Store apps (no `mas` currently)
- Credentials (handled by 1Password)
