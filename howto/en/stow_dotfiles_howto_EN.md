---
title: Managing Dotfiles with Git and GNU Stow
lang: en
tags: [git, dotfiles, stow]
created: 2024-10-12
updated: 2025-07-27
---

# 🚀 Practical Guide: Managing Dotfiles with Git and GNU Stow

## 🎯 Goal

This how‑to shows how to centralize and deploy your personal configuration files (dotfiles) using a **Git** repository together with **GNU Stow** so you can share them effortlessly across machines.

## 🧰 Requirements
### 🖥️ Environment
- Recommended OS: Linux / macOS / Windows WSL


### 📦 Required tools

- **git**
- **stow**

### Quick install

- **Debian / Ubuntu:**  
  ```bash
  sudo apt update && sudo apt install git stow
  ```
- **Arch Linux:**  
  ```bash
  sudo pacman -S git stow
  ```
- **Fedora:**  
  ```bash
  sudo dnf install git stow
  ```

## Repository layout

```text
dotfiles/
├── zsh/
│   └── .zshrc
└── kitty/
    └── .config/
        └── kitty/
            └── kitty.conf
```

Each sub‑folder is named after a program or “module”.  
Internal paths mirror what Stow should reproduce inside **$HOME**.

## Example dotfiles

### .zshrc

```zsh
# ~/.zshrc — minimal example
export ZSH="$HOME/.oh-my-zsh"
ZSH_THEME="agnoster"

plugins=(git)

source $ZSH/oh-my-zsh.sh

# Convenience
setopt AUTO_CD        # auto‑cd into folders
setopt HIST_IGNORE_DUPS
```

### kitty.conf

```conf
# ~/.config/kitty/kitty.conf — minimal example
font_family      Iosevka
font_size        13.0
cursor_shape     block

# Colors
background       #1e1e2e
foreground       #cdd6f4
```

## Deploying with GNU Stow

From inside the repository:

```bash
cd ~/dotfiles

# Create symlinks
stow zsh      # installs ~/.zshrc
stow kitty    # installs ~/.config/kitty
```

Remove a package:

```bash
stow -D kitty   # cleanly removes the symlinks
```

## Distribution notes

| Distribution   | Notes                                                       |
|----------------|-------------------------------------------------------------|
| Debian / Ubuntu| `$HOME/.config` is created by default; no extra steps.      |
| Arch Linux     | Git and Stow are in *core*; pacman handles dependencies.    |
| Fedora         | Package `stow` lives in the official repos.                 |

## Daily workflow

1. Edit a file in the relevant module.  
2. Run `git add -p && git commit -m "Update dotfile"`.  
3. Push to GitHub, GitLab or your forge of choice.  
4. On a new machine: clone the repo and run `stow <module>`.

## Conclusion

Combining **Git** and **GNU Stow** gives you:

- Clear version history for your configs.
- Rapid installation through simple symlinks.
- The ability to enable/disable sets of dotfiles selectively.

Happy hacking!
