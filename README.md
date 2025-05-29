# NEW Install

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
```bash

# Installing ohmyzsh

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

### reboot the system

# BUILDING PYTHON

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
sudo make -j$(nproc)
sudo make altinstall  # Important: Use altinstall, not install
```

## ZRAM

### Install zram
```bash
sudo apt update
sudo apt install zram-tools

sudo nano /etc/default/zramswap
```

### Example settings (you can leave it as is)
```text
# How much RAM percentage should be used
PERCENT=50

# Max number of zram devices (usually = number of cores)
ZRAM_DEVICES=1

# Set algorithm (lz4 is faster, zstd compresses better)
ALGO=lz4

```

### Enable and start zram
```bash
sudo systemctl enable --now zramswap
```

### Verify ZRAM is active
```bash
swapon --show
```

