# New Install

# ohmyzsh

```bash
sudo apt-get install zsh
sudo apt-get install git
sudo chsh -s $(which zsh)
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```
# install UV

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### Replace .zshrc with following

```bash
# =============================================================
# PATH
# =============================================================
export PATH="/sbin:/usr/sbin:$HOME/.local/bin:$PATH"

# =============================================================
# Oh My Zsh (keep, but trim it)
# =============================================================
export ZSH="$HOME/.oh-my-zsh"
ZSH_THEME="bira"

# Disable git status in prompt for large repos (speeds up prompt a lot)
DISABLE_UNTRACKED_FILES_DIRTY="true"

plugins=(git docker docker-compose)
source $ZSH/oh-my-zsh.sh

# =============================================================
# HISTORY  (big upgrade — OMZ defaults are weak)
# =============================================================
HISTSIZE=50000
SAVEHIST=50000
HISTFILE="$HOME/.zsh_history"
setopt HIST_IGNORE_ALL_DUPS   # no duplicate entries
setopt HIST_IGNORE_SPACE      # commands prefixed with space won't be saved
setopt SHARE_HISTORY          # share history across all open terminals
setopt HIST_REDUCE_BLANKS     # trim blanks

# =============================================================
# NAVIGATION & SHELL BEHAVIOR
# =============================================================
setopt AUTO_CD                # type a dir name to cd into it
setopt CORRECT                # suggest corrections for typos
setopt NO_CASE_GLOB           # case-insensitive globbing

# =============================================================
# ALIASES
# =============================================================
alias cls='clear'
alias ls='ls -alh --color=auto'   # -h = human-readable sizes
alias ..='cd ..'
alias ...='cd ../..'

alias up='sudo apt update && sudo apt upgrade -y'

# Docker Compose
alias dcup='docker compose up -d --build'
alias dcdown='docker compose down'
alias dcrun='docker compose run --rm'
alias dclogs='docker compose logs -f'
alias dsp='docker system prune -f'

# =============================================================
# FUNCTIONS
# =============================================================

# mkcd: make a directory and immediately cd into it
mkcd() { mkdir -p "$1" && cd "$1"; }

# extract: one command to extract any archive type
extract() {
  case "$1" in
    *.tar.gz|*.tgz)  tar xzf "$1" ;;
    *.tar.bz2)        tar xjf "$1" ;;
    *.zip)            unzip "$1"   ;;
    *.gz)             gunzip "$1"  ;;
    *.rar)            unrar x "$1" ;;
    *)  echo "'$1' cannot be extracted via extract()" ;;
  esac
}

# =============================================================
# ENV (keep at the bottom)
# =============================================================
. "$HOME/.local/bin/env"
```

# Add SSH Key to GitHub from Debian

##  Generate a new SSH key

```bash
ssh-keygen -t ed25519 -C "$(whoami)@$(hostname)"
```

## Start the ssh-agent and add your key

```bash
# Start the agent in the background & Add your key to the agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

## Copy the public key

```bash
cat ~/.ssh/id_ed25519.pub
```

## Add key to GitHub

 - Log in to: https://github.com
- Go to: Settings → SSH and GPG keys
- Click: New SSH key
  - Title: e.g. Debian Optiplex
  - Key: paste the contents of id_ed25519.pub
- Click Add SSH Key.

## Test the connection

```bash
ssh -T git@github.com
```

# Install Docker

## Step 1 — Update Debian

Inside the LXC:

```bash
apt update && apt upgrade -y
apt install -y ca-certificates curl gnupg lsb-release
```

---

## Step 2 — Add Docker official repository

```bash
mkdir -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/debian/gpg \
  | gpg --dearmor -o /etc/apt/keyrings/docker.gpg

chmod a+r /etc/apt/keyrings/docker.gpg
```

Add repo:

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo $VERSION_CODENAME) stable" \
  > /etc/apt/sources.list.d/docker.list
```

---

## Step 3 — Install Docker Engine + Compose plugin

```bash
apt update

apt install -y docker-ce docker-ce-cli containerd.io \
  docker-buildx-plugin docker-compose-plugin
```

---

## Step 4 — Start & enable Docker

```bash
systemctl enable docker
systemctl start docker
```

Verify:

```bash
docker --version
docker compose version
```

---

## Step 5 — Run without sudo (optional)

```bash
usermod -aG docker $USER
newgrp docker
```

---

## Step 6 — Test

```bash
docker run hello-world
```
