---
title: Guide pratique : Gérer vos dotfiles avec Git et GNU Stow
lang: fr
tags: [git, dotfiles, stow]
created: 2025-07-27
updated: 2025-07-27
---

# 🚀 Guide pratique : Gérer vos dotfiles avec Git et GNU Stow

## 🎯 Objectif

Ce tutoriel explique comment centraliser et déployer vos fichiers de configuration (“dotfiles”) à l’aide d’un dépôt **Git** et de **GNU Stow**, afin de les partager facilement entre plusieurs machines.

## Prérequis

### Logiciels nécessaires

- **git**
- **stow**

### Installation rapide

- **Debian / Ubuntu :**  
  ```bash
  sudo apt update && sudo apt install git stow
  ```
- **Arch Linux :**  
  ```bash
  sudo pacman -S git stow
  ```
- **Fedora :**  
  ```bash
  sudo dnf install git stow
  ```

## Organisation du dépôt

```text
dotfiles/
├── zsh/
│   └── .zshrc
└── kitty/
    └── .config/
        └── kitty/
            └── kitty.conf
```

Chaque sous‑dossier porte le nom d’un programme ou d’un “module”.  
Les chemins internes reflètent l’arborescence attendue dans **$HOME**.

## Exemple de dotfiles

### .zshrc

```zsh
# ~/.zshrc — exemple minimal
export ZSH="$HOME/.oh-my-zsh"
ZSH_THEME="agnoster"

plugins=(git)

source $ZSH/oh-my-zsh.sh

# Options de confort
setopt AUTO_CD        # cd direct sans 'cd'
setopt HIST_IGNORE_DUPS
```

### kitty.conf

```conf
# ~/.config/kitty/kitty.conf — exemple minimal
font_family      Iosevka
font_size        13.0
cursor_shape     block

# Couleurs
background       #1e1e2e
foreground       #cdd6f4
```

## Déploiement avec GNU Stow

Positionnez‑vous dans le dépôt :

```bash
cd ~/dotfiles

# Créer les liens symboliques
stow zsh      # installe ~/.zshrc
stow kitty    # installe ~/.config/kitty
```

Pour retirer un paquet :

```bash
stow -D kitty   # supprime proprement les symlinks
```

## Particularités selon la distribution

| Distribution      | Remarques                                                    |
|-------------------|--------------------------------------------------------------|
| Debian / Ubuntu   | `$HOME/.config` existe déjà ; pas d’étape supplémentaire.    |
| Arch Linux        | Git et Stow sont dans *core* ; pacman gère les dépendances.  |
| Fedora            | Le paquet `stow` est dans les dépôts officiels.              |

## Workflow quotidien

1. Modifiez un fichier dans le sous‑dossier concerné.  
2. Exécutez : `git add -p && git commit -m "Mise à jour dotfile"`.  
3. Poussez sur GitHub, GitLab ou votre forge favorite.  
4. Sur une nouvelle machine : clonez le dépôt et lancez `stow <module>`.

## Conclusion

En combinant **Git** et **GNU Stow**, vous obtenez :

- Une historisation claire de vos configurations.
- Une installation rapide via de simples liens symboliques.
- La possibilité d’activer ou de désactiver sélectivement des ensembles de dotfiles.

Bon hack !
