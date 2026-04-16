---
title: "My Mac Setup 🍎"
date: 2025-04-22
hero: mac_neuf.webp
description: What I configure every time I reset my Mac
theme: Toha
---

I love setting up my computers. So much so that I reset everything two or three times a year. Over time, I’ve gotten into the habit of noting down the essentials I need to feel comfortable.

I could have used [Nix](https://nix.dev/) to have a config-as-code, but I find the learning curve too steep. So I fall back on the good old [Homebrew](https://brew.sh) to reinstall everything manually.

Maybe one day I’ll have the will to use Nix properly—who knows?

{{< vs 4 >}}

<p align="center">
  {{< img src="/posts/vie-perso/config-mac/mac_neuf.webp" width="700" align="center" alt="Illustration of a Mac and a childlike character on the right in a Firefox Windows XP window" >}}
</p>

{{< vs 4 >}}

## Pasta Names 🍝

I ALWAYS name my computer after a type of pasta as a little tribute to:
- (currently) *lu*__*mac*__*he* – 2023
- __*mac*__*aroni* – 2022
- lasagne – 2018
- tagliatelle – 2015
- farfalle – 2014

{{< vs 4 >}}

## The packages I install

I need a package manager. Since Homebrew is the most popular, it’s the one I always use. So I run:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Then I install my essentials listed in a Brewfile:

```bash
echo '
  tap "azure/kubelogin"
  tap "hashicorp/tap"
  tap "homebrew/services"
  tap "kreuzwerker/taps"
  tap "mongodb/brew"
  tap "oven-sh/bun"

  # Automate deployment, configuration, and upgrading
  brew "ansible"
  # Automatic configure script builder
  brew "autoconf"
  # Collection of over 500 reusable autoconf macros
  brew "autoconf-archive"
  # Tool for generating GNU Standards-compliant Makefiles
  brew "automake"
  # Text processing scripting language
  brew "awk"
  # Azure Storage data transfer utility
  brew "azcopy"
  # Microsoft Azure CLI 2.0
  brew "azure-cli"
  # Open-source formatting library for C++
  brew "fmt"
  # Object-file caching compiler wrapper
  brew "ccache"
  # Cross-platform make
  brew "cmake"
  # Play, record, convert, and stream audio and video
  brew "ffmpeg"
  # Package compiler and linker metadata toolkit
  brew "pkgconf"
  # Duplicate file utility
  brew "czkawka"
  # Perl lib for reading and writing EXIF metadata
  brew "exiftool"
  # Modern, maintained replacement for ls
  brew "eza"
  # Like neofetch, but much faster because written mostly in C
  brew "fastfetch"
  # Command-line fuzzy finder written in Go
  brew "fzf"
  # Open source programming language to build simple/reliable/efficient software
  brew "go"
  # Kubernetes package manager
  brew "helm"
  # Deploy Kubernetes Helm Charts
  brew "helmfile"
  # Configurable static site generator
  brew "hugo"
  # Kubernetes CLI To Manage Your Clusters In Style!
  brew "k9s"
  # Kubernetes command-line interface
  brew "kubernetes-cli"
  # Tool that can switch between kubectl contexts easily and create aliases
  brew "kubectx"
  # Platform built on V8 to build network applications
  brew "node"
  # MongoDB Shell to connect, configure, query, and work with your MongoDB database
  brew "mongosh"
  # Netwide Assembler (NASM) is an 80x86 assembler
  brew "nasm"
  # Small build system for use with gyp or CMake
  brew "ninja"
  # Development kit for the Java programming language
  brew "openjdk"
  # Execute binaries from Python packages in isolated environments
  brew "pipx"
  # PDF rendering library (based on the xpdf-3.0 code base)
  brew "poppler"
  # Object-relational database system
  brew "postgresql@14"
  # Safe, concurrent, practical language
  brew "rust"
  # Terminal multiplexer with VT100/ANSI terminal emulation
  brew "screen"
  # Display directories as trees (with optional color/HTML output)
  brew "tree"
  # Subversion-like utility to work with Jackrabbit FileVault
  brew "vault-cli"
  # Executes a program periodically, showing output fullscreen
  brew "watch"
  # Command-line tool to help build, run, and test web extensions
  brew "web-ext"
  # Linter for YAML files
  brew "yamllint"
  # Feature-rich command-line audio/video downloader
  brew "yt-dlp"
  # Shell extension to navigate your filesystem faster
  brew "zoxide"
  # A Kubernetes credential (exec) plugin implementing azure authentication
  brew "azure/kubelogin/kubelogin"
  # Terraform
  brew "hashicorp/tap/terraform"
  # Vault
  brew "hashicorp/tap/vault"
  # High-performance, schema-free, document-oriented database
  brew "mongodb/brew/mongodb-community"
  # Incredibly fast JavaScript runtime, bundler, transpiler and package manager - all in one.
  brew "oven-sh/bun/bun"
  # Menu bar tool to limit maximum charging percentage
  cask "aldente"
  # Android SDK component
  cask "android-platform-tools"
  # Server and cloud storage browser
  cask "cyberduck"
  # App to build and share containerised applications and microservices
  cask "docker"
  # Utility for customizing which browser to start
  cask "finicky"
  # File archiver
  cask "keka"
  # Open-source software for live streaming and screen recording
  cask "obs"
  # Retro video game emulation
  cask "openemu"
  # Move and resize windows using keyboard shortcuts or snap areas
  cask "rectangle"
  # Tool to reverse the direction of scrolling
  cask "scroll-reverser"
  # Instant messaging application focusing on security
  cask "signal"
  # Remote access and connectivity software focused on security
  cask "teamviewer"
  # Multimedia player
  cask "vlc"
  # Binary releases of VS Code without MS branding/telemetry/licensing
  cask "vscodium"

  # Extensions vscode
  vscode "bradlc.vscode-tailwindcss"
  vscode "burkeholland.simple-react-snippets"
  vscode "eamodio.gitlens"
  vscode "github.copilot"
  vscode "github.copilot-chat"
  vscode "mightbesimon.emoji-icons"
  vscode "ms-python.debugpy"
  vscode "ms-python.python"
  vscode "pascalreitermann93.vscode-yaml-sort"
  vscode "usernamehw.errorlens"
  vscode "vscode-icons-team.vscode-icons"
  vscode "yunduo.color-highlight-css-color-4"
' > ./Brewfile

brew bundle install --file=./Brewfile
```

{{< vs 4 >}}

Create your own `Brewfile` by running this command:

{{< vs 4 >}}

```bash
brew bundle dump --describe --file=./Brewfile
```

{{< vs 4 >}}

## Oh My ZSH

I install [Oh-my-zsh](https://ohmyz.sh/) to get a beautiful terminal:

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Plugin that reminds me if a shortcut for a command exists
git clone https://github.com/MichaelAquilina/zsh-you-should-use.git $ZSH_CUSTOM/plugins/you-should-use

# Plugin that suggests the command as you type
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# Plugin that colors syntax
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

{{< vs 2 >}}

The installer will configure `~/.zshrc`. Personally, I use this setup:

```bash
ZSH_THEME="fino-time"
plugins=( git zsh-autosuggestions zsh-syntax-highlighting zsh-z you-should-use )
```

{{< vs 2 >}}

### fino-time.zsh-theme

With a little twist for the fino-time theme (if you decide to go with it). I often modify its file in `~/.oh-my-zsh/themes/fino-time.zsh-theme`:

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

This way it:
- displays my current Kubernetes namespace  
- changes the icons  
- when I run `export STREAM=true`, it hides the namespace (in case I need to share my screen)  

{{< vs 4 >}}

<p align="center">
  {{< img src="/posts/vie-perso/config-mac/fino-time.webp" width="700" align="center" alt="Screenshot of the prompt with the fino-time theme" >}}
  <p style="text-align: center;"><i>The rendering of my prompt</i></p>
</p>

{{< vs 4 >}}

## Configuration files

{{< vs 2 >}}

### .zshrc

Your `rc` file should already be well populated with all these tools (pipx, oh-my-zsh, fzf). I also need my little shortcuts to go faster:

- `code` to quickly open VSCodium  
- `g` for Git  
- `tf` for Terraform  
- `ls` and `ll` to list my directories with eza  
- `cdd` to move more quickly to my favorite directory  
- `mcd` to create and enter a folder  
- `dock_lock` to prevent accidentally enlarging the Mac dock  
- `update` to update everything at once  
- `con` and `tun` for my SSH shortcuts  
- kubectl aliases (e.g., `kgpo` for `kubectl get pod`, etc.)  

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

So in my ZSH scripts folder, you’ll find `kubectl_aliases.sh` [available here](https://github.com/ahmetb/kubectl-aliases), to which I’ve added:
- alias kns='kubens'
- alias kctx='kubectx'
- alias kgcj='kubectl get cronjob' (and the equivalents)

{{< vs 4 >}}

### ~/.ssh/config

I won’t detail everything in it, but this is quite useful. It helps avoid getting disconnected every two minutes without SSH activity:

```bash
Host *
    ServerAliveInterval 120
    ServerAliveCountMax 60
    TCPKeepAlive yes
```

{{< vs 4 >}}

### ~/.gitconfig

It’s simpler to use Git with a global configuration!

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

If you have any questions or suggestions, feel free to contact me by [email](mailto:timothe@chauvet.cloud), on [LinkedIn](https://www.linkedin.com/in/timothechauvet/), or directly by opening [an issue on GitHub](https://github.com/timothechauvet/timothechauvet.github.io/issues).