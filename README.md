# New Install

# ohmyzsh

```bash
sudo apt-get install zsh
sudo chsh -s $(which zsh)
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

### Edit .zshrc

```bash
nano ~/.zshrc
```

# install UV

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### Change theme:

```text
ZSH_THEME="bira"
```

### Add following text at the end

```text
#CUSTOM ALIAS
alias cls=clear
alias ls='ls -al'

alias up.up='sudo apt update && sudo apt upgrade -y'

alias dcupdb='docker compose up -d --build'
alias dcdown='docker compose down'
alias dcrun='docker compose run --rm'
alias dsp='docker system prune'

export PATH="/sbin:/usr/sbin:$PATH"
```



# ZRAM

## Install zram

```bash
sudo apt update
sudo apt install -y zram-tools
```

## Edit config

```bash
sudo nano /etc/default/zramswap
```

## Example settings (you can leave it as is)

```text
# How much RAM percentage should be used
PERCENT=50

# Max number of zram devices (usually = number of cores)
ZRAM_DEVICES=1

# Set algorithm (lz4 is faster, zstd compresses better)
ALGO=lz4

```

## Enable and start zram

```bash
sudo systemctl enable --now zramswap
```

## Verify ZRAM is active

```bash
swapon --show
```

# Add SSH Key to GitHub from Debian

##  Generate a new SSH key

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
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

sudo systemctl restart ollama
```
