---
title: "Ma config sur Mac 🍎"
date: 2024-12-16
hero: k3s.webp
description: Ce que je configure à chaque fois que je réinitialise mon mac
theme: Toha
---

J'adore configurer mes ordinateurs. Si bien que je réinitialise tout, 2 ou 3 fois par an. Alors à force, j'ai pris l'habitude de noter les essentiels dont j'ai besoin pour être à l'aise.

J'aurais pu utiliser [Nix](https://nix.dev/) pour avoir une config-as-code, mais je trouve la courbe d'apprentissage trop pentue. Alors je me rabats sur le bon vieux [Homebrew](https://brew.sh) pour tout réinstaller manuellement.

Peut-être qu'un jour j'aurai la foi d'utiliser Nix correctement, qui sait ?

{{< vs 4 >}}

## Noms de pates 🍝

Je nomme TOUJOURS mon ordinateur avec un nom de pates. Un petit hommage à :
- (actuellement) *lu*__*mac*__*he* - 2023
- __*mac*__*aroni* - 2022
- lasagne - 2018
- tagliatelle - 2015
- farfalle - 2014

{{< vs 4 >}}

## Les packages que j'installe

il me faut un gestionnaire de packages. Homebrew étant le + populaire, c'est celui que j'utilise systématiquement. Je lance donc `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"` pour l'installer.

Puis j'installe mes essentiels que voici :

```bash
packages=(
  # Logiciels pour bosser
  ansible 
  azure-cli 
  kubectx 
  kubelogin 
  kubernetes-cli 
  mongosh 
  # Codé en rust, supprime les doublons photos/vidéos
  czkawka 
  # Détaille les données exif d'un fichier
  exiftool 
  # Remplace ls et ll
  eza 
  # Remplace neofetch pour imprimer les specs de mon ordinateur
  fastfetch 
  # Convertisseur vidéo/audio
  ffmpeg 
  # Formatteur
  fmt 
  # Sélectionner dans une liste
  fzf 
  # Mon blog
  go 
  hugo 
  # Pour coder
  node 
  pipx 
  rust 
  # Lister les fichiers récursivement
  tree 
  # Rafraichir toutes les 2s une commande
  watch 
  # Télécharger des vidéos YouTube
  yt-dlp
)

cask_packages=(
  # Stoppe le chargement à 80% pour protéger la batterie
  aldente 
  # Pour bosser 
  docker 
  # Permet de choisir le navigateur selon le site qu'on clique
  finicky 
  # .Zip
  keka 
  # Capture d'écran
  obs 
  # Pour remplacer les commandes par défaut de gestion des fenêtres
  rectangle 
  # Pour avoir une souris et un trackpad et scroller naturellement
  scroll-reverser 
  # Chat
  signal 
  # Vidéos
  vlc 
  # Alternative OSS et bloat-less de VSCode
  vscodium
)

brew install "${packages[@]}"
brew install --cask "${cask_packages[@]}"
```

{{< vs 4 >}}

## Oh my ZSH

J'installe [Oh-my-zsh](https://ohmyz.sh/) pour avoir un splendide terminal

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Plugin qui me rappelle si un raccourci d'une commande existe
git clone https://github.com/MichaelAquilina/zsh-you-should-use.git $ZSH_CUSTOM/plugins/you-should-use

# Plugin qui suggère la commande en train d'être écrite
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# Plugin qui colorie la syntaxe
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

{{< vs 2 >}}

L'installateur va configurer `~/.zshrc`. Perso j'utilise cette config

```bash
ZSH_THEME="fino-time"
plugins=( git zsh-autosuggestions zsh-syntax-highlighting zsh-z you-should-use )
```

{{< vs 2 >}}

### fino-time.zsh-theme

Avec un petit twist pour le thème fino-time ([si vous décidez de partir avec lui](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes)). Je modifie souvent son fichier dans `~/.oh-my-zsh/themes/fino-time.zsh-theme`

```bash
# [...]
function kubernetes_namespace {
  local ns=$(kubectl config view --minify --output 'jsonpath={..namespace}')
  [ -n "$ns" ] && echo "%{$fg[141]%}☸️  $ns%{$reset_color%} "
}

function mac_icon {
  echo -e "\ue711"
}

PROMPT="╭─%{$FG[040]%} \$(mac_icon) Mac%{$reset_color%} %{$terminfo[bold]$FG[226]%}%~%{$reset_color%}\$(git_prompt_info)\$(ruby_prompt_info) %D{%d/%m/%Y %H:%M} %{$FG[141]%}\${STREAM:+}\$(${STREAM:+:false}\${STREAM:-kubernetes_namespace})%{$reset_color%}
╰─\$(virtualenv_info)\$(prompt_char) "

ZSH_THEME_GIT_PROMPT_PREFIX=" %{$FG[239]%}on%{$reset_color%} %{$fg[255]%}"
ZSH_THEME_GIT_PROMPT_SUFFIX="%{$reset_color%}"
ZSH_THEME_GIT_PROMPT_DIRTY="%{$FG[202]%}✘"
ZSH_THEME_GIT_PROMPT_CLEAN="%{$FG[040]%}✔"
ZSH_THEME_RUBY_PROMPT_PREFIX=" %{$FG[239]%}using%{$FG[243]%} ‹"
ZSH_THEME_RUBY_PROMPT_SUFFIX="›%{$reset_color%}"
```

{{< vs 2 >}}

Comme ça il 
- affiche mon namespace Kubernetes courant
- change les icônes
- quand je fais `export STREAM=true`, il cache le namespace (si jamais je dois partager mon écran)

{{< img src="/posts/vie-perso/config-mac/fino-time.webp" width="400" align="left" alt="Le rendu de mon prompt" >}}

{{< vs 4 >}}

## .zshrc

Votre fichier `rc` doit déjà être bien rempli avec tous ces logiciels (pipx, oh-my-zsh, fzf). J'ai aussi besoin de mes petits raccourcis pour aller + vite

- `code` pour ouvrir VSCodium rapidement
- `g` pour Git
- `tf` pour Terraform
- `ls` et `ll` pour lister mes dossiers avec eza
- `cdd` pour me déplacer + rapidement dans mon répertoire favori
- `mcd` pour créer et rentrer dans un dossier
- `dock_lock` pour ne pas agrandir le dock mac par erreur
- `update` pour tout mettre à jour en même temps
- `con` et `tun` pour mes raccourcis SSH
- les aliases kubectl (`kgpo` pour `kubectl get pod` etc...)

```bash
# Oh my ZSH
export ZSH="$HOME/.oh-my-zsh"
ZSH_THEME="fino-time"
plugins=( git zsh-autosuggestions zsh-syntax-highlighting zsh-z you-should-use )
source $ZSH/oh-my-zsh.sh

# Autocomplete
autoload -U compinit; compinit

# Aliases
alias code="codium"
alias g="git"
alias tf="terraform"
alias ls='eza $1 --icons'
alias ll="ls -lahG"
alias cdd="cd ~/Documents/Developpement"
alias dock_lock="defaults write com.apple.Dock size-immutable -bool true; killall Dock"
alias dock_unlock="defaults delete com.apple.Dock size-immutable; killall Dock"

# Update & cleanup
alias update='
    BLUE="\033[0;34m"
    ITALIC="\033[3m"
    RESET="\033[0m"

    echo -e "${BLUE}${ITALIC}Updating Homebrew...${RESET}" &&
    brew update && brew upgrade &&
    
    echo -e "${BLUE}${ITALIC}Cleaning up Homebrew...${RESET}" &&
    brew cleanup &&
    
    echo -e "${BLUE}${ITALIC}Updating macOS Software...${RESET}" &&
    softwareupdate -i -a &&
    omz update &&
    
    echo -e "${BLUE}${ITALIC}Clearing System Cache (requires sudo)...${RESET}" &&
    BEFORE_SIZE=$(du -sh ~/Library/Caches/ 2>/dev/null | cut -f1) &&
    sudo find ~/Library/Caches/ -mindepth 1 -delete 2>/dev/null || true &&
    AFTER_SIZE=$(du -sh ~/Library/Caches/ 2>/dev/null | cut -f1) &&
    echo "Space freed in ~/Library/Caches/: $BEFORE_SIZE -> $AFTER_SIZE" &&

    BEFORE_SIZE=$(du -sh /Library/Caches/ 2>/dev/null | cut -f1) &&
    sudo find /Library/Caches/ -mindepth 1 -delete 2>/dev/null || true &&
    AFTER_SIZE=$(du -sh /Library/Caches/ 2>/dev/null | cut -f1) &&
    echo "Space freed in /Library/Caches/: $BEFORE_SIZE -> $AFTER_SIZE" &&
    
    echo -e "${BLUE}${ITALIC}Cleaning Adobe Media Cache...${RESET}" &&
    BEFORE_SIZE=$(du -sh ~/Library/Application\ Support/Adobe/Common/Media\ Cache\ Files/ 2>/dev/null | cut -f1) &&
    sudo find ~/Library/Application\ Support/Adobe/Common/Media\ Cache\ Files/ -mindepth 1 -delete 2>/dev/null || true &&
    AFTER_SIZE=$(du -sh ~/Library/Application\ Support/Adobe/Common/Media\ Cache\ Files/ 2>/dev/null | cut -f1) &&
    echo "Space freed in Adobe Media Cache: $BEFORE_SIZE -> $AFTER_SIZE" &&

    echo -e "${BLUE}${ITALIC}Cleaning Adobe General Cache...${RESET}" &&
    BEFORE_SIZE=$(du -sh ~/Library/Caches/Adobe/ 2>/dev/null | cut -f1) &&
    sudo find ~/Library/Caches/Adobe/ -mindepth 1 -delete 2>/dev/null || true &&
    AFTER_SIZE=$(du -sh ~/Library/Caches/Adobe/ 2>/dev/null | cut -f1) &&
    echo "Space freed in Adobe Cache: $BEFORE_SIZE -> $AFTER_SIZE" &&
    
    echo -e "${BLUE}${ITALIC}Cleaning Adobe Temp Files...${RESET}" &&
    BEFORE_SIZE=$(du -sh ~/Library/Application\ Support/Adobe/ 2>/dev/null | cut -f1) &&
    sudo find ~/Library/Application\ Support/Adobe/ -name "*Temp*" -delete 2>/dev/null || true &&
    AFTER_SIZE=$(du -sh ~/Library/Application\ Support/Adobe/ 2>/dev/null | cut -f1) &&
    echo "Space freed in Adobe Temp: $BEFORE_SIZE -> $AFTER_SIZE" &&
    
    echo -e "${BLUE}${ITALIC}Cleaning Photoshop Temporary Files...${RESET}" &&
    BEFORE_SIZE=$(du -sh /tmp/ 2>/dev/null | cut -f1) &&
    sudo find /tmp/ -name "Photoshop Temp*" -delete 2>/dev/null || true &&
    AFTER_SIZE=$(du -sh /tmp/ 2>/dev/null | cut -f1) &&
    echo "Space freed in Photoshop Temp: $BEFORE_SIZE -> $AFTER_SIZE" &&
    
    echo -e "${BLUE}${ITALIC}Showing fastfetch...${RESET}" &&
    fastfetch &&
    
    echo -e "${BLUE}${ITALIC}Update complete!${RESET}"'

# fzf
source <(fzf --zsh)

# mcd
mcd() { mkdir -p "$@" 2> >(sed s/mkdir/mcd/ 1>&2) && cd "$_"; }

# Custom scripts
source ~/.zsh_scripts/kubectl_aliases.sh

# Created by `pipx` on 2024-12-21 13:35:29
export PATH="$PATH:/Users/lumache/.local/bin"
```

{{< vs 2 >}}

### ~/.zsh_scripts

Donc dans mon dossier de scripts ZSH on retrouve kubectl_aliases.sh [disponible ici](https://github.com/ahmetb/kubectl-aliases), et auquel j'ai rajouté
  - alias kns='kubens'
  - alias kctx='kubectx'
  - alias kgcj='kubectl get cronjob' (et les équivalents)

{{< vs 4 >}}

## ~/.ssh/config

Je ne vais pas vous détailler tout ce qu'il y a dedans, mais ça c'est plutôt utile. C'est pour ne pas se faire déconnecter toutes les 2 minutes sans interaction ssh :

```bash
Host *
    ServerAliveInterval 120
    ServerAliveCountMax 60
    TCPKeepAlive yes
```

{{< vs 4 >}}

## ~/.gitconfig

C'est plus simple d'utiliser Git avec une configuration globale !

```toml
[alias]
	ci = commit
	co = checkout
[user]
	name = timothechauvet
	email = timothe@chauvet.cloud
[push]
	autoSetupRemote = true
```

---

Si vous avez des questions ou des suggestions, n'hésitez pas à me contacter par [mail](mailto:timothe@chauvet.cloud), sur [LinkedIn](https://www.linkedin.com/in/timothechauvet/) ou directement en envoyant [une *issue* sur GitHub](https://github.com/timothechauvet/timothechauvet.github.io/issues)