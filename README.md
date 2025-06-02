# New Install

## Install sudo and add user to it

```bash
su -
<enter password here>
apt install -y sudo
usermod -aG sudo your_username
```

## then reboot

```bash
sudo apt install -y git curl htop glances ncdu
```

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
alias python=python3.12
alias pyt=python3.12
alias py=python3.12
alias py3=python3.12

alias dcupdb='docker compose up -d --build'
alias dcdown='docker compose down'
alias dcrun='docker compose run --rm'
alias dsp='docker system prune'

export PATH="/sbin:/usr/sbin:$PATH"
```

reboot the system

# PYTHON 3.12

## 1. Install build dependencies

```bash
sudo apt update
sudo apt install -y wget build-essential zlib1g-dev libncurses5-dev \
  libgdbm-dev libnss3-dev libssl-dev libreadline-dev libffi-dev \
  libsqlite3-dev libbz2-dev liblzma-dev tk-dev uuid-dev
```

## 2. Download the latest Python source

```bash
cd /usr/src
sudo wget https://www.python.org/ftp/python/3.12.3/Python-3.12.3.tgz
sudo tar -xf Python-3.12.3.tgz
cd Python-3.12.3
```

## 3. Build Python (without messing with system python)

```bash
sudo ./configure --enable-optimizations --with-ensurepip=install
```

```bash
sudo make -j$(nproc)
```

```bash
sudo make altinstall
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

## Set up Docker's apt repository.

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

## Install latest Docker packages

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

### Post Install steps

```bash
sudo groupadd docker
sudo usermod -aG docker $USER
```

Logout and login into the box

```bash
docker run hello-world
```

# Install Ollama

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

## Optimize Ollama Configuration (for old PC running linux)

Create systemd service with optimizations:

```bash
sudo nano /etc/systemd/system/ollama.service
```

Add this configuration:

```ini
[Unit]
Description=Ollama Service
After=network-online.target

[Service]
ExecStart=/usr/local/bin/ollama serve
User=ollama
Group=ollama
Restart=always
RestartSec=3
Environment="OLLAMA_HOST=0.0.0.0"
Environment="OLLAMA_NUM_PARALLEL=1"
Environment="OLLAMA_MAX_LOADED_MODELS=1"
Environment="OLLAMA_FLASH_ATTENTION=0"
Environment="OLLAMA_KEEP_ALIVE=5m"
Environment="OLLAMA_MAX_QUEUE=1"

[Install]
WantedBy=default.target
```

Then reload and restart:

```bash
sudo systemctl daemon-reload
sudo systemctl restart ollama
```
