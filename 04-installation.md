# Chapter 4: Installation and Setup

*In which we discover that installing terminal multiplexers is easier than explaining what they do to your non-technical friends*

## The Pre-Installation Checklist

Before we dive into the installation, let's make sure you have everything you need:

1. **A Terminal**: If you don't have this, we need to have a different conversation
2. **Admin/sudo access**: Or a very understanding system administrator
3. **5 minutes**: Seriously, that's all it takes
4. **Patience**: Just kidding, you won't need this

## The Universal Truth About Installation

Here's the beautiful thing about Screen and Tmux: chances are, one of them is already installed on your system. It's like finding money in your pocket, except it's a terminal multiplexer.

Quick check:
```bash
# Check for Screen
$ screen -v
Screen version 4.09.00 (GNU) 30-Jan-22

# Check for Tmux
$ tmux -V
tmux 3.3a

# If you see version numbers, congratulations! Skip ahead!
```

## Installing GNU Screen

### macOS (The Shiny Aluminum Way)

```bash
# Using Homebrew (the civilized way)
$ brew install screen

# Using MacPorts (for the contrarians)
$ sudo port install screen

# From source (for the masochists)
$ curl -O https://ftp.gnu.org/gnu/screen/screen-4.9.0.tar.gz
$ tar -xzf screen-4.9.0.tar.gz
$ cd screen-4.9.0
$ ./configure
$ make
$ sudo make install
```

Fun fact: macOS used to ship with Screen pre-installed, but Apple removed it because... Apple.

### Linux (The Thousand Flavors)

#### Debian/Ubuntu (The Popular Kids)
```bash
$ sudo apt update
$ sudo apt install screen

# Feeling adventurous? Get the latest version
$ sudo apt install build-essential
$ wget https://ftp.gnu.org/gnu/screen/screen-4.9.0.tar.gz
# ... follow source installation steps
```

#### Red Hat/CentOS/Fedora (The Enterprise Squad)
```bash
# Modern Fedora
$ sudo dnf install screen

# CentOS/RHEL/Old Fedora
$ sudo yum install screen

# From EPEL (when main repos fail you)
$ sudo yum install epel-release
$ sudo yum install screen
```

#### Arch Linux (BTW I Use Arch)
```bash
$ sudo pacman -S screen

# From AUR (because of course)
$ yay -S screen-git  # or your favorite AUR helper
```

#### Alpine Linux (The Docker Darling)
```bash
$ apk add screen
```

#### Gentoo (For Those Who Like to Watch Progress Bars)
```bash
$ emerge --ask app-misc/screen
# Go make coffee, maybe dinner too
```

### BSD Systems (The Unix Purists)

```bash
# FreeBSD
$ pkg install screen

# OpenBSD
$ pkg_add screen

# NetBSD
$ pkgin install screen
```

### Windows (The Plot Twist)

Yes, you can run Screen on Windows. No, we're not sure why you would, but here's how:

#### WSL (Windows Subsystem for Linux)
```bash
# Inside WSL (basically just Linux)
$ sudo apt install screen
```

#### Cygwin
1. Download Cygwin installer
2. Select 'screen' package during installation
3. Question your life choices
4. Use Screen

#### Git Bash
Screen doesn't work well here. This is the universe telling you something.

## Installing Tmux

### macOS (Now With More Hipster Appeal)

```bash
# Homebrew (still the way)
$ brew install tmux

# MacPorts (still for contrarians)
$ sudo port install tmux

# From source (still for masochists)
$ git clone https://github.com/tmux/tmux.git
$ cd tmux
$ sh autogen.sh
$ ./configure
$ make
$ sudo make install
```

### Linux (The Same Thousand Flavors)

#### Debian/Ubuntu
```bash
$ sudo apt update
$ sudo apt install tmux

# Want the latest? Add a PPA
$ sudo add-apt-repository ppa:pi-rho/dev
$ sudo apt update
$ sudo apt install tmux
```

#### Red Hat/CentOS/Fedora
```bash
# Fedora
$ sudo dnf install tmux

# CentOS/RHEL
$ sudo yum install tmux

# Too old? EPEL to the rescue
$ sudo yum install epel-release
$ sudo yum install tmux
```

#### Arch Linux
```bash
$ sudo pacman -S tmux

# Bleeding edge from AUR
$ yay -S tmux-git
```

#### Alpine Linux
```bash
$ apk add tmux
```

#### Gentoo
```bash
$ emerge --ask app-misc/tmux
# Progress bars intensify
```

### BSD Systems

```bash
# FreeBSD
$ pkg install tmux

# OpenBSD (Tmux's birthplace!)
$ pkg_add tmux  # Probably already installed

# NetBSD
$ pkgin install tmux
```

### Windows

#### WSL
```bash
$ sudo apt install tmux
# Just works™
```

#### Cygwin
1. Run Cygwin installer
2. Search for 'tmux'
3. Install
4. Marvel that it actually works

## Building From Source (Because You're Hardcore)

### Building Screen

```bash
# Get the source
$ wget https://ftp.gnu.org/gnu/screen/screen-4.9.0.tar.gz
$ tar -xzf screen-4.9.0.tar.gz
$ cd screen-4.9.0

# Configure with options
$ ./configure --prefix=/usr/local \
              --enable-colors256 \
              --enable-rxvt_osc \
              --enable-telnet

# Build it
$ make

# Test it (optional but recommended)
$ ./screen -v

# Install it
$ sudo make install
```

Common configure options:
- `--enable-pam`: PAM support (for the paranoid)
- `--enable-use-locale`: Better international support
- `--enable-colors256`: Because 16 colors is so 1987

### Building Tmux

```bash
# Dependencies first!
# Debian/Ubuntu
$ sudo apt install libevent-dev ncurses-dev build-essential bison pkg-config

# macOS (with Homebrew)
$ brew install libevent ncurses pkg-config automake

# Get the source
$ git clone https://github.com/tmux/tmux.git
$ cd tmux

# Build it
$ sh autogen.sh
$ ./configure
$ make

# Install it
$ sudo make install
```

## Post-Installation Setup

### Setting Up Screen

Create your `.screenrc`:
```bash
$ cat > ~/.screenrc << 'EOF'
# Basic Screen configuration
startup_message off
defscrollback 10000
vbell off
hardstatus alwayslastline
hardstatus string '%{= kG}%-Lw%{= kW}%50> %n%f* %t%{= kG}%+Lw%< %{= kG}%-=%D %m/%d %C%a'
EOF
```

Test your installation:
```bash
$ screen -S test
# Press Ctrl+A d to detach
$ screen -ls  # Should show your session
$ screen -r test  # Reattach
```

### Setting Up Tmux

Create your `.tmux.conf`:
```bash
$ cat > ~/.tmux.conf << 'EOF'
# Basic Tmux configuration
set -g default-terminal "screen-256color"
set -g history-limit 10000
set -g base-index 1
set -g pane-base-index 1
set -g mouse on
EOF
```

Test your installation:
```bash
$ tmux new -s test
# Press Ctrl+B d to detach
$ tmux ls  # Should show your session
$ tmux attach -t test  # Reattach
```

## Version Management

### When Your Package Manager Betrays You

Sometimes the packaged version is ancient. Here's how to get the latest:

#### Using Linuxbrew (Homebrew for Linux)
```bash
# Install Linuxbrew
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Add to PATH
$ echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"' >> ~/.profile
$ eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"

# Install latest versions
$ brew install screen tmux
```

#### Using Conda
```bash
# Install Miniconda first
$ conda install -c conda-forge screen tmux
```

#### Using Nix
```bash
# Install Nix package manager
$ curl -L https://nixos.org/nix/install | sh

# Install packages
$ nix-env -iA nixpkgs.screen nixpkgs.tmux
```

## Container Images (Because It's 2024)

### Docker Images

```dockerfile
# Dockerfile with both Screen and Tmux
FROM ubuntu:latest
RUN apt-get update && \
    apt-get install -y screen tmux && \
    apt-get clean
CMD ["/bin/bash"]
```

```bash
# Build and run
$ docker build -t multiplex .
$ docker run -it multiplex
```

### Pre-built Images
```bash
# Just Tmux
$ docker run -it --rm instrumentisto/tmux

# Development environment with multiplexers
$ docker run -it --rm nixos/nix nix-shell -p screen tmux
```

## Verification and Troubleshooting

### Common Installation Issues

#### "Command not found" After Installation
```bash
# Check if it's in a non-standard location
$ which screen
$ which tmux

# Update PATH if necessary
$ export PATH="/usr/local/bin:$PATH"
$ echo 'export PATH="/usr/local/bin:$PATH"' >> ~/.bashrc
```

#### Permission Denied
```bash
# Screen needs setuid for multiuser mode
$ sudo chmod +s /usr/bin/screen

# But maybe don't do this unless you really need multiuser
```

#### Missing Dependencies
```bash
# For Screen
$ ldd $(which screen)  # Check what's missing

# For Tmux
$ ldd $(which tmux)

# Common missing library: libevent
$ sudo apt install libevent-dev  # Debian/Ubuntu
$ sudo yum install libevent-devel  # Red Hat/CentOS
```

### Testing Your Installation

Create a test script:
```bash
#!/bin/bash
# test-multiplexer.sh

echo "Testing Screen..."
screen -S test-screen -d -m echo "Screen works!"
sleep 1
screen -ls

echo -e "\nTesting Tmux..."
tmux new -s test-tmux -d 'echo "Tmux works!"'
sleep 1
tmux ls

echo -e "\nCleaning up..."
screen -S test-screen -X quit 2>/dev/null
tmux kill-session -t test-tmux 2>/dev/null

echo "All tests passed!"
```

## Platform-Specific Quirks

### macOS Quirks
- Terminal.app doesn't support true colors (use iTerm2)
- Clipboard integration needs `reattach-to-user-namespace`
- System Integrity Protection might block some features

### Linux Quirks
- Some distributions have ancient versions in repos
- SELinux might interfere (disable or configure properly)
- Different distributions put config files in different places

### WSL Quirks
- Copy/paste integration can be wonky
- Some features don't work in WSL1 (use WSL2)
- Windows Terminal is actually pretty good for this

## Choosing Your Default Multiplexer

Make Screen or Tmux start automatically:

```bash
# In ~/.bashrc or ~/.zshrc

# Auto-start Tmux
if command -v tmux &> /dev/null && [ -z "$TMUX" ]; then
    tmux attach -t default || tmux new -s default
fi

# OR auto-start Screen
if command -v screen &> /dev/null && [ -z "$STY" ]; then
    screen -RR
fi
```

Warning: This can be annoying if you don't always want a multiplexer.

## The "Installing on Ancient Systems" Guide

Sometimes you're stuck with CentOS 5 or some other archaeological find:

```bash
# Screen from source (works almost everywhere)
$ wget https://ftp.gnu.org/gnu/screen/screen-4.9.0.tar.gz
$ tar -xzf screen-4.9.0.tar.gz
$ cd screen-4.9.0
$ ./configure --prefix=$HOME/local
$ make
$ make install
$ export PATH=$HOME/local/bin:$PATH

# Tmux on ancient systems (good luck)
# First, build libevent from source
# Then ncurses from source
# Then Tmux from source
# Then cry a little
```

## Conclusion: You're Ready!

Congratulations! You now have one (or both) terminal multiplexers installed. You're ready to join the elite club of people who:
- Never lose work to disconnections
- Can leave work running overnight
- Have way too many terminal windows open
- Judge others for using regular terminals

The installation is the easy part. The hard part is explaining to your colleagues why you have 47 Tmux panes open and insist it's "organized."

---

*Next: [Chapter 5 - Basic Commands and Navigation](05-basic-commands.md)*

*In which we learn the sacred key combinations that separate us from the terminal peasants*