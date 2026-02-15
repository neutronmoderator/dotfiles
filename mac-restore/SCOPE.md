# Mac Restore Tool -- Scope

A single script (`restore.sh`) that takes a fresh macOS install and reproduces this exact setup. Idempotent -- safe to re-run. Paired with `snapshot.sh` to capture current state.

---

## 1. Package managers

### Homebrew
- Install Homebrew if missing
- Restore taps (12 total):
  `barutsrb/tap`, `felixkratz/formulae`, `homebrew/autoupdate`,
  `jsattler/tap`, `koekeishiya/formulae`, `minicodemonkey/chief`,
  `rgerganov/footswitch`, `sasha-computer/tap`, `steipete/tap`,
  `theboredteam/boring-notch`, `tobi/try`, `yakitrak/yakitrak`
- `brew bundle install` from `Brewfile` (167 formulas, 38 casks)
- `brew autoupdate start` (LaunchAgent already handled by homebrew-autoupdate)

### uv tools
- `domain-search`, `git-filter-repo`, `nano-pdf`, `pi-fzf`, `pif-cli`

### Bun global packages
- Everything in `~/.bun/install/global/node_modules/`
- Snapshot captures `package.json`, restore runs `bun install -g`

### ~/.local/bin scripts
- 19 scripts/binaries: `agent`, `crabwalk`, `cursor-agent`, `domain-search`,
  `toggle-dark-mode`, `transcribe`, `x-likes-fzf`, `zb`, etc.
- Snapshot copies them; restore symlinks or copies back

---

## 2. Shell (fish)

- Install fish via Homebrew (in Brewfile)
- Set as default shell (`chsh -s /opt/homebrew/bin/fish`)
- Fish config from `sources/config.fish`
- Shell aliases (ported from `shell.nix`):
  - Git: `gl`, `gcm`, `gaa`, `gs`, `gfp`, `gpl`, `gp`, `gc`, `gcb`, `gcl`, `grv`
  - Navigation: `ll`, `cdd`, `cc`, `z.`, `cfg`
- No fish plugins currently (no fisher)

---

## 3. Dotfiles / configs

### ~/.config/ (all 23 dirs)

| App | Source | Notes |
|-----|--------|-------|
| 1Password | `~/.config/1Password/` | SSH agent config |
| borders | `~/.config/borders/` | Window borders |
| btop | `~/.config/btop/` | System monitor |
| fish | `~/.config/fish/` | Shell config |
| footswitch | `~/.config/footswitch/` | Foot pedal mappings |
| gh | `~/.config/gh/` | GitHub CLI (config.yml, hosts.yml has auth) |
| ghostty | `~/.config/ghostty/` | Terminal config |
| karabiner | `~/.config/karabiner/` | Keyboard remapping (Caps Lock -> Hyper/Escape) |
| linearmouse | `~/.config/linearmouse/` | Mouse settings |
| mole | `~/.config/mole/` | Clean list |
| nia | `~/.config/nia/` | API key (via 1Password, not in repo) |
| ntfy | `~/.config/ntfy/` | Notification client |
| nvim | `~/.config/nvim/` | Neovim config |
| op | `~/.config/op/` | 1Password CLI |
| opencode | `~/.config/opencode/` | Config |
| pdfx | `~/.config/pdfx/` | PDF highlights |
| qmd | `~/.config/qmd/` | Index |
| raycast | `~/.config/raycast/` | Extensions + AI config |
| skhd | `~/.config/skhd/` | Hotkey daemon |
| uv | `~/.config/uv/` | Python package manager |
| yabai | `~/.config/yabai/` | Tiling WM |
| zed | `~/.config/zed/` | Editor (settings + keymap) |
| zellij | `~/.config/zellij/` | Terminal multiplexer |

### Home dotfiles

| Path | Notes |
|------|-------|
| `~/.gitconfig` | Git config + SSH signing |
| `~/.ssh/config` | 1Password agent sock, keychain |
| `~/.claude/` | CLAUDE.md, settings.json, commands/, skills/, context/ |
| `~/.claude.json` | Claude Code auth/config |
| `~/.pi/` | Pi agent config, skills, extensions |
| `~/.boss/` | Boss CLI config |
| `~/.boundless/` | Team tool config |
| `~/.chief/` | Team tool config |
| `~/.cursor/` | Cursor editor config |
| `~/.hammerspoon/` | init.lua (52 lines) + Spoons/ |
| `~/.profile` | Shell profile |
| `~/.hushlogin` | Suppress login banner |
| `~/.fly/` | Fly.io CLI auth |
| `~/.railway/` | Railway CLI auth |

---

## 4. Fonts

20 fonts in `~/Library/Fonts/`:
- Berkeley Mono Variable (Regular, Italic)
- Bookerly family (14 variants)
- BookerlyMono family (6 variants)

Snapshot copies the font files; restore drops them back.

---

## 5. macOS system preferences

### defaults write commands

| Domain | Key settings |
|--------|-------------|
| `com.apple.dock` | autohide=1, pinned apps (Ghostty, Helium, Discord, Spotify), Downloads folder, mru-spaces=0 |
| `com.apple.finder` | Show extensions, default view |
| `com.apple.WindowManager` | Stage Manager off, tiled margins off, click-to-show-desktop off, hide desktop on |
| `NSGlobalDomain` | KeyRepeat=2, InitialKeyRepeat=15, natural scroll=off, reduce motion=on |
| `com.apple.screencapture` | Format, location |
| `com.apple.controlcenter` | Menu bar autohide=3, status item positions |
| `com.apple.menuextra.clock` | Clock format |
| `com.apple.spaces` | Spaces config |
| `com.apple.symbolichotkeys` | Disabled system shortcuts |
| `com.apple.Spotlight` | Spotlight categories enabled/disabled |

### Appearance
- Light mode (no AppleInterfaceStyle set)
- Default accent color (multicolor)
- Reduce motion: ON
- Reduce transparency: OFF

### Trackpad
- Tap to click: ON
- Natural scrolling: OFF
- Force click: ON

### Default apps (file associations)
- Video files (mp4, avi, mkv, mov, wmv, ts) -> IINA
- Audio files (mp3, wav, flac) -> VLC
- Default browser -> Helium
- Snapshot via `com.apple.LaunchServices` plist

### Security
- Firewall: OFF (document as intentional or fix)
- FileVault: OFF
- Gatekeeper: disabled

### Power management
- Battery: display sleep 1min, disk sleep 10min, system sleep off
- Reduce motion: on

---

## 6. Login items + LaunchAgents

### Login items (System Settings)
AlDente, Raycast, LinearMouse, Thaw, Vivid, FineTune, boringNotch, CCUsage, DockDoor

### User LaunchAgents (`~/Library/LaunchAgents/`)
| Plist | Purpose |
|-------|---------|
| `com.agentsc.email-sync.plist` | Email sync |
| `com.agentsc.pi-daily.plist` | Pi daily task |
| `com.sasha.x-sync.plist` | X/Twitter sync |
| `sh.ntfy.typefully.plist` | Typefully notifications |
| `Handy.plist` | Handy app |
| `com.github.domt4.homebrew-autoupdate.plist` | Brew auto-update (managed by brew) |
| `com.google.*.plist` | Google updater (managed by Chrome) |

---

## 7. System extensions

- Karabiner DriverKit VirtualHIDDevice (1.8.0)
- Tailscale Network Extension (1.94.2)

---

## 8. Accessibility permissions (20 apps)

borders, skhd, yabai, OmniWM, DockDoor, Discord, Ice, Logi,
LinearMouse, Ghostty, Handy, Raycast, Thaw, UnnaturalScrollWheels,
ScreenStudio, Claude Desktop, Hammerspoon, boringNotch

Cannot be granted programmatically -- restore script prints a checklist.

---

## 9. Network

- Tailscale configured and running
- WiFi: 7Old
- DNS: system default (no custom DNS)
- SSH config: 1Password agent + keychain

---

## 10. App-specific preferences

| App | Domain | Notes |
|-----|--------|-------|
| Ghostty | `com.mitchellh.ghostty` | Window positions etc. |
| IINA | `com.colliderli.iina` | Player prefs |
| Raycast | `com.raycast.macos` | Extensions, hotkeys, snippets |
| LinearMouse | `com.lujjjh.LinearMouse` | Scroll/accel curves |
| Hammerspoon | `org.hammerspoon.Hammerspoon` | Window management |
| Vivid | `com.goodsnooze.vivid` | Display brightness |

---

## 11. Text replacements

| Shortcut | Expansion |
|----------|-----------|
| `omw` | On my way! |
| `k11` | sasha_kindle11@kindle.com |
| `@addr` | 7 Old Post Office Walk, Surbiton, London KT6 4JF |
| `@me` | Sasha, Developer Relations Engineer, Boundless, ... |

Stored in `NSGlobalDomain NSUserDictionaryReplacementItems`.

---

## 12. Dock layout

Pinned apps (left to right): Ghostty, Helium, Discord, Spotify
Folders: Downloads

---

## 13. Explicitly out of scope

- iCloud state / Apple ID sign-in
- Time Machine backups
- Notification preferences (binary plist, fragile across OS versions)
- Browser bookmarks/extensions (managed separately)
- App Store purchases (no `mas` apps currently)
- Credentials / API keys (1Password handles these)
- Finder sidebar favorites
- Wallpaper (can be set manually)
- Apple Shortcuts (empty)

---

## Design

```
dotfiles/
├── mac-restore/
│   ├── restore.sh          # Main entry point (idempotent)
│   ├── snapshot.sh          # Captures current state into this dir
│   ├── Brewfile             # Auto-generated by snapshot
│   ├── defaults.sh          # macOS defaults write commands
│   ├── fonts/               # ~/Library/Fonts/ copy
│   ├── launch-agents/       # ~/Library/LaunchAgents/ copies
│   ├── local-bin/           # ~/.local/bin/ scripts
│   ├── text-replacements.plist  # NSUserDictionaryReplacementItems
│   ├── file-associations.plist  # LaunchServices handlers
│   ├── dock.plist           # Dock layout
│   └── configs/             # Snapshotted ~/.config/* dirs
├── sources/                 # Shared config files (also used by NixOS)
└── ...
```

### Two commands

**`snapshot.sh`** -- run on current Mac:
1. `brew bundle dump --force`
2. Copy all `~/.config/` dirs
3. Copy home dotfiles (`.gitconfig`, `.ssh/config`, `.claude/`, `.pi/`, `.hammerspoon/`, etc.)
4. Copy `~/Library/Fonts/`
5. Copy `~/Library/LaunchAgents/` (skip Google/brew-managed ones)
6. Copy `~/.local/bin/` scripts
7. Export `defaults` for each domain into `defaults.sh`
8. Export text replacements
9. Export file associations
10. Export Dock layout
11. List uv tools + bun globals into manifest files
12. Git add + commit

**`restore.sh`** -- run on fresh Mac:
1. Install Xcode CLI tools
2. Install Homebrew
3. `brew bundle install` (taps, formulas, casks)
4. Set fish as default shell
5. Symlink/copy all dotfiles
6. Install fonts
7. Apply `defaults.sh`
8. Set file associations
9. Set Dock layout + killall Dock
10. Install uv tools
11. Install bun globals
12. Copy `~/.local/bin/` scripts
13. Copy LaunchAgents + `launchctl load`
14. Apply text replacements
15. Print manual checklist (ordered by dependency):

    **Step 1 -- 1Password (everything depends on this)**
    - Sign into 1Password in the browser + desktop app
    - Enable the SSH agent in 1Password settings
    - Run `op signin` to authenticate the CLI
    - This unlocks: git signing, SSH, gh auth, fly/railway tokens, API keys

    **Step 2 -- Authenticate CLIs (need 1Password first)**
    - `gh auth login` (GitHub CLI)
    - `fly auth login` (Fly.io)
    - `railway login` (Railway)
    - Retrieve API keys from 1Password vault for: nia, ntfy, etc.

    **Step 3 -- System extensions (need to approve in System Settings)**
    - Open Karabiner-Elements (triggers DriverKit install prompt)
    - Open Tailscale, sign in, approve network extension

    **Step 4 -- Accessibility permissions (20 apps)**
    System Settings > Privacy & Security > Accessibility:
    - Window management: yabai, skhd, borders, Hammerspoon
    - Input: Karabiner, LinearMouse, UnnaturalScrollWheels, footswitch
    - Utilities: Raycast, Ghostty, DockDoor, boringNotch, Ice, Thaw
    - Other: Discord, Handy, ScreenStudio, Claude Desktop, OmniWM, Logi

    **Step 5 -- Login items**
    System Settings > General > Login Items, add:
    AlDente, Raycast, LinearMouse, Thaw, Vivid, FineTune,
    boringNotch, CCUsage, DockDoor

    **Step 6 -- Sign into apps**
    - Helium (browser -- sync bookmarks)
    - Discord
    - Spotify
    - iCloud / Apple ID (if not done during macOS setup)
    - Raycast (import settings + extensions)

    **Step 7 -- Verify security posture**
    - FileVault: decide whether to enable (currently OFF)
    - Firewall: decide whether to enable (currently OFF)
    - Gatekeeper: currently disabled (intentional for dev tools)

### Principles
- Idempotent: every step checks before acting
- No secrets in repo: API keys in 1Password, auth tokens excluded
- Symlinks where possible (configs point back to repo)
- Brewfile is single source of truth for packages
- `defaults` commands are explicit, not blind plist dumps
- Auth files (gh hosts.yml, fly/railway tokens, nia API key) excluded, noted in manual steps
