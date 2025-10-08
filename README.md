# VS Code User Settings Repository

A version-controlled backup of my personal Visual Studio Code user profile (settings, keybindings, extensions list, snippets, and supporting metadata). This repository lets me:

- Reproduce my development environment on any machine quickly
- Track changes over time (git diff for settings/keybindings)
- Share a curated list of extensions
- Experiment safely (branch for new themes / keymaps)

> NOTE: This repository lives at / symlinked into the VS Code user configuration directory so that changes made inside VS Code are picked up by git.

---
## 📁 Repository Contents Overview

| Path / File | Purpose |
|-------------|---------|
| `settings.json` | Global user preferences (editor, UI, language features). |
| `keybindings.json` | Custom keyboard shortcuts. |
| `vscode_extenstions.txt` | (Curated) list of extension identifiers to install. |
| `snippets/` | User snippets (language-specific or global). |

---
## ✅ What You Typically Want to Track

Things to keep:
- `settings.json`
- `keybindings.json`
- `snippets/`
- `vscode_extenstions.txt`

Excluded by the current `.gitignore`:
- `workspaceStorage/`
- `globalStorage/`
- `History/`
- `sync/`
- OS cruft like `.DS_Store`, `Thumbs.db`, `desktop.ini`
- Logs `*.log`
- Build artifacts: `build/`, `dist/`, `out/` (forward-looking in case you add scripts/tools here)
- Dependency directories: `node_modules/`, `vendor/` (not normally present in a pure settings repo, but covered defensively)
- IDE metadata: `.idea/`, Vim swap files `*.swp`, `*.swo`
- Most of `.vscode/` except the core JSON config files (pattern allows `settings.json`, `tasks.json`, `launch.json`, `extensions.json` if such a folder ever appears here)

---
## Quick Environment Rebuild (Fresh Machine)

1. Install VS Code from https://code.visualstudio.com/
2. Clone this repository:
   - Linux / macOS:
     ```bash
     git clone <repo_url> ~/.config/Code/User
     ```
   - Windows (PowerShell):
     ```pwsh
     git clone <repo_url> "$Env:APPDATA/Code/User"
     ```
   (If the directory already exists, back it up first — see below.)
3. Install extensions:
   ```pwsh
   Get-Content vscode_extenstions.txt | ForEach-Object {
     if($_ -and -not $_.StartsWith('#')) { code --install-extension $_ }
   }
   ```
4. Restart VS Code.

---
## 🪄 Alternative: Symlink Strategy
Instead of cloning directly into the live settings folder, you can keep the repo elsewhere and symlink the `User` directory.

- Windows (PowerShell, run as Administrator if needed):
  ```pwsh
  $src = "C:/path/to/repo"
  $dest = "$Env:APPDATA/Code/User"
  Rename-Item $dest "$dest.backup_$(Get-Date -f yyyyMMddHHmmss)" -ErrorAction SilentlyContinue
  cmd /c mklink /J "$dest" "$src"
  ```
- macOS:
  ```bash
  mv "~/Library/Application Support/Code/User" "~/Library/Application Support/Code/User.backup" 2>/dev/null || true
  ln -s /path/to/repo "~/Library/Application Support/Code/User"
  ```
- Linux:
  ```bash
  mv ~/.config/Code/User ~/.config/Code/User.backup 2>/dev/null || true
  ln -s /path/to/repo ~/.config/Code/User
  ```

---
## 🛟 Back Up Existing Settings (Before Overwriting)

Windows (PowerShell):
```pwsh
$src = "$Env:APPDATA/Code/User"
$backup = "$src.backup_$(Get-Date -f yyyyMMddHHmmss)"
Copy-Item $src $backup -Recurse
```

macOS / Linux:
```bash
cp -a ~/.config/Code/User ~/.config/Code/User.backup_"$(date +%Y%m%d%H%M%S)"
```

---
## 🔄 Keeping Extensions List Up To Date
Export currently installed extension IDs into `vscode_extenstions.txt` (note: filename has a small typo; you may optionally rename to `vscode_extensions.txt` for clarity—if you do, update references in this README and any scripts):

- PowerShell:
  ```pwsh
  code --list-extensions | Sort-Object | Set-Content vscode_extenstions.txt
  ```
- Bash:
  ```bash
  code --list-extensions | sort > vscode_extenstions.txt
  ```

Then commit the update:
```bash
git add vscode_extenstions.txt
git commit -m "chore: update extensions list"
```

You can annotate the list with comments (lines starting with `#`) for categories or rationale.

Example snippet:
```
# Productivity
usernamehw.errorlens
streetsidesoftware.code-spell-checker

# Git
mhutchie.git-graph

# AI
GitHub.copilot
GitHub.copilot-chat
```

---
## 🧩 Snippets
Add new snippet files under `snippets/`. File naming convention:
- `language.json` for language-specific (e.g., `python.json`)
- `global.code-snippets` for global

---
## 🧰 Optional Automation
Add a simple setup script (future enhancement):
```
setup.ps1   # Clones repo, installs extensions
setup.sh    # *nix equivalent
```
Add a GitHub Action to validate `vscode_extenstions.txt` formatting.

---
## 🧷 Useful Paths (Reference)
| OS | Path |
|----|------|
| Windows | `%APPDATA%/Code/User` |
| macOS | `~/Library/Application Support/Code/User` |
| Linux | `~/.config/Code/User` |

---
## 📌 Roadmap / TODO Ideas
- Add `setup.ps1` / `setup.sh`
- Add secret scanning pre-commit hook
- Categorize extensions file with sections
- Cull large ephemeral folders or ignore them
- Add export script to auto-commit weekly diffs