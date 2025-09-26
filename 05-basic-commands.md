# Chapter 5: Basic Commands and Navigation

*In which we learn the secret handshakes, mystic incantations, and finger gymnastics required to bend terminal multiplexers to our will*

## The Prefix Key: Your Gateway Drug

Both Screen and Tmux use a "prefix key" concept. It's like saying "Simon Says" before every command, except Simon is your terminal multiplexer and it actually listens.

- **Screen**: `Ctrl+A` (because A is for Awesome... or just first in the alphabet)
- **Tmux**: `Ctrl+B` (because B comes after A, and Tmux came after Screen)

Think of the prefix key as shifting into "command mode." You're telling the multiplexer, "Hey, the next key is for you, not the application running inside you."

## The Command Rosetta Stone

Let's translate between Screen and Tmux, because learning one set of commands is hard enough:

| Action | Screen | Tmux | Mnemonic |
|--------|--------|------|----------|
| Create window | `Ctrl+A c` | `Ctrl+B c` | **c**reate |
| Next window | `Ctrl+A n` | `Ctrl+B n` | **n**ext |
| Previous window | `Ctrl+A p` | `Ctrl+B p` | **p**revious |
| List windows | `Ctrl+A "` | `Ctrl+B w` | **w**indows (Tmux wins here) |
| Rename window | `Ctrl+A A` | `Ctrl+B ,` | **A**ppellation vs comma? |
| Kill window | `Ctrl+A k` | `Ctrl+B &` | **k**ill vs... ampersand? |
| Detach | `Ctrl+A d` | `Ctrl+B d` | **d**etach (they agree!) |
| Split horizontal | `Ctrl+A S` | `Ctrl+B "` | **S**plit vs quotation |
| Split vertical | `Ctrl+A |` | `Ctrl+B %` | Pipe vs percent |
| Switch pane | `Ctrl+A Tab` | `Ctrl+B arrows` | Tab vs actually intuitive |

## Session Management: The Basics

### Creating Sessions Like a Pro

#### Screen
```bash
# Basic session
$ screen

# Named session (for humans)
$ screen -S project-x

# Start detached (ninja mode)
$ screen -dmS background-job

# With a command
$ screen -S monitor htop
```

#### Tmux
```bash
# Basic session
$ tmux

# Named session (more readable)
$ tmux new -s project-x

# Start detached (also ninja mode)
$ tmux new -d -s background-job

# With a command
$ tmux new -s monitor 'htop'
```

### The Art of Detachment

Detaching is like putting your work in suspended animation. Your processes keep running, blissfully unaware that you've gone to lunch, home, or to contemplate existence.

**Both use**: `Prefix + d`

The screen shows:
- Screen: `[detached]`
- Tmux: `[detached (from session session-name)]`

It's like saying goodbye, but with the promise you'll be back.

### Listing Your Sessions (Finding Your Lost Children)

#### Screen
```bash
$ screen -ls
There are screens on:
    31829.project-x    (Detached)
    28925.monitoring    (Attached)
    12345.pts-0.hostname    (Detached)  # The one you forgot about
```

Those numbers? Process IDs. That `.pts-0.hostname`? Screen's way of naming things when you forget to.

#### Tmux
```bash
$ tmux ls
project-x: 3 windows (created Thu Oct 26 14:22:33 2023)
monitoring: 1 windows (created Thu Oct 26 10:15:22 2023) (attached)
```

Look at that! Actual useful information! Window counts! Timestamps! Tmux showing off again.

### Reattaching (The Reunion)

#### Screen
```bash
# Single session
$ screen -r

# Multiple sessions (by name)
$ screen -r project-x

# Multiple sessions (by PID)
$ screen -r 31829

# Force detach and reattach (for the impatient)
$ screen -D -r project-x
```

#### Tmux
```bash
# Most recent session
$ tmux attach

# Specific session
$ tmux attach -t project-x

# Short form
$ tmux a -t project-x

# Create or attach
$ tmux new -A -s project-x
```

## Window Navigation: Tab Management for Terminals

Windows are like browser tabs, but for terminals. Too many, and you're lost. Too few, and you're constantly context-switching.

### Creating Windows

After pressing the prefix key:
- **Screen**: `c` (creates window with shell)
- **Tmux**: `c` (also creates window with shell)

Finally, something they agree on!

### Navigating Windows

#### The Sequential Approach
- **Next**: `Prefix + n`
- **Previous**: `Prefix + p`
- **Last**: `Prefix + l` (Tmux) or `Prefix + Prefix` (Screen)

#### The Direct Approach
- **Screen**: `Prefix + 0-9` (for windows 0-9)
- **Tmux**: `Prefix + 0-9` (same!)

#### The Interactive Approach
- **Screen**: `Prefix + "` (shows list, navigate with arrows)
- **Tmux**: `Prefix + w` (shows preview, navigate with arrows)

Tmux's window preview is like window shopping for terminals - you can see what's inside before committing.

### Renaming Windows (Because "bash" Gets Old)

#### Screen
```bash
Prefix + A
# Type new name
# Press Enter
```

#### Tmux
```bash
Prefix + ,
# Type new name
# Press Enter
```

Pro tip: Name your windows by function: "server", "logs", "database", "stackoverflow", "panic".

## Pane Management: Split Personality Disorder

Panes are windows within windows. It's like Inception, but for terminals.

### Creating Splits

#### Screen
```bash
# Horizontal split
Ctrl+A S  # Capital S

# Vertical split (v4.1+)
Ctrl+A |  # Pipe character

# New window in split
Ctrl+A Tab  # Move to new region
Ctrl+A c    # Create window
```

#### Tmux
```bash
# Horizontal split
Ctrl+B "  # Quote mark

# Vertical split
Ctrl+B %  # Percent sign

# Smart people remap these:
# In ~/.tmux.conf:
# bind | split-window -h
# bind - split-window -v
```

### Navigating Panes

#### Screen
```bash
Ctrl+A Tab  # Cycle through regions
# That's it. That's the option.
```

#### Tmux
```bash
Ctrl+B arrows     # Navigate directionally
Ctrl+B q          # Show pane numbers
Ctrl+B q [0-9]    # Jump to pane number
Ctrl+B ;          # Go to last pane
Ctrl+B o          # Cycle through panes

# With vim muscle memory:
# In ~/.tmux.conf:
# bind h select-pane -L
# bind j select-pane -D
# bind k select-pane -U
# bind l select-pane -R
```

### Resizing Panes (Because Size Matters)

#### Screen
```bash
Ctrl+A :resize +5   # Make region 5 lines bigger
Ctrl+A :resize -5   # Make region 5 lines smaller
Ctrl+A :resize 20   # Set region to 20 lines
```

#### Tmux
```bash
# Hold Ctrl+B, then use arrow keys
Ctrl+B : resize-pane -U 5  # Up 5 lines
Ctrl+B : resize-pane -D 5  # Down 5 lines
Ctrl+B : resize-pane -L 5  # Left 5 columns
Ctrl+B : resize-pane -R 5  # Right 5 columns

# Or the lazy way:
Ctrl+B Alt+arrows  # Resize in that direction
```

### The Zoom Feature (Tmux Only)

```bash
Ctrl+B z  # Toggle zoom on current pane
```

This makes the current pane full screen temporarily. Perfect for when your coworker is looking over your shoulder and you need to hide those other panes.

## Copy Mode: When Your Mouse Betrays You

Both multiplexers have a copy mode, because sometimes you need to copy text and your mouse is all the way over there.

### Screen's Copy Mode

```bash
Ctrl+A [         # Enter copy mode
# Navigate with arrows or vim keys
Space            # Start selection
Space            # End selection and copy
Ctrl+A ]         # Paste

# Scrolling
Ctrl+U           # Half page up
Ctrl+D           # Half page down
g                # Go to top
G                # Go to bottom
```

### Tmux's Copy Mode

```bash
Ctrl+B [         # Enter copy mode
# Navigate with arrows or vim keys
Space            # Start selection
Enter            # Copy selection
Ctrl+B ]         # Paste

# Scrolling
Page Up/Down     # Scroll pages
g                # Go to top
G                # Go to bottom

# Enable vi mode for better experience:
# In ~/.tmux.conf:
# setw -g mode-keys vi
```

## The Command Line: When Keybindings Aren't Enough

Both multiplexers have a command mode for when you need to get fancy:

### Screen's Command Mode
```bash
Ctrl+A :         # Enter command mode
# Examples:
screen -t logs 2 tail -f /var/log/syslog  # Create named window
source ~/.screenrc                        # Reload config
quit                                      # Kill everything
```

### Tmux's Command Mode
```bash
Ctrl+B :         # Enter command mode
# Examples:
new-window -n logs 'tail -f /var/log/syslog'  # Create named window
source ~/.tmux.conf                            # Reload config
kill-server                                    # Nuclear option
```

## Quick Reference Survival Card

Print this out and tape it to your monitor until muscle memory kicks in:

### Daily Driver Commands

```bash
# Session Management
Start:      screen -S name     | tmux new -s name
List:       screen -ls          | tmux ls
Attach:     screen -r name      | tmux attach -t name
Detach:     Ctrl+A d            | Ctrl+B d

# Window Management
Create:     Ctrl+A c            | Ctrl+B c
Next:       Ctrl+A n            | Ctrl+B n
Previous:   Ctrl+A p            | Ctrl+B p
List:       Ctrl+A "            | Ctrl+B w
Rename:     Ctrl+A A            | Ctrl+B ,

# Pane Management
Split H:    Ctrl+A S            | Ctrl+B "
Split V:    Ctrl+A |            | Ctrl+B %
Navigate:   Ctrl+A Tab          | Ctrl+B arrows
Close:      Ctrl+A X            | Ctrl+B x

# Copy Mode
Enter:      Ctrl+A [            | Ctrl+B [
Paste:      Ctrl+A ]            | Ctrl+B ]
```

## Common Workflows

### The Developer Setup
```bash
# Create a dev session
$ tmux new -s dev

# Window 0: Editor
$ vim .

# Create Window 1: Server
Ctrl+B c
$ npm run dev

# Create Window 2: Git
Ctrl+B c
$ git status

# Navigate between them
Ctrl+B n/p  # or Ctrl+B 0/1/2
```

### The System Admin Dashboard
```bash
# Create monitoring session
$ screen -S monitor

# Window 0: htop
$ htop

# Window 1: Logs (split into panes)
Ctrl+A c
$ tail -f /var/log/syslog
Ctrl+A S
Ctrl+A Tab
Ctrl+A c
$ tail -f /var/log/auth.log

# Window 2: Network
Ctrl+A c
$ iftop
```

### The "Oh Shit" Recovery
```bash
# Connection died, session still running
$ tmux ls  # or screen -ls
$ tmux attach  # or screen -r

# Multiple attached sessions (someone else is watching)
$ tmux attach -d  # Force detach others
$ screen -D -r    # Screen equivalent
```

## Finger Gymnastics and Ergonomics

After a week of using terminal multiplexers, you'll develop:
1. **Prefix Pinky**: Unusual strength in your pinky finger
2. **Control Thumb**: On Mac, remapping Caps Lock to Control saves lives
3. **The Claw**: The unique hand position for Ctrl+A or Ctrl+B

Consider these ergonomic improvements:
```bash
# Remap prefix to Ctrl+Space (easier to reach)
# Screen: ~/.screenrc
escape ^@^@

# Tmux: ~/.tmux.conf
unbind C-b
set-option -g prefix C-Space
bind-key C-Space send-prefix
```

## The Learning Curve

Week 1: "How do I exit this thing?"
Week 2: "Okay, I can create windows"
Week 3: "Panes are actually useful"
Week 4: "I have 47 sessions running and I regret nothing"

## Practice Exercises

1. **The Speed Run**: Create a session, make 3 windows, name them, detach, reattach, close everything. Time yourself.

2. **The Pane Game**: Create a 2x2 grid of panes. Put a different command in each (htop, logs, ping, etc.).

3. **The Copy Challenge**: Without using your mouse, copy your system's IP address from `ifconfig` output and paste it into a new file.

4. **The Recovery Drill**: Start a long-running process, force-close your terminal, then recover the session.

## Conclusion: You're Dangerous Now

With these commands, you're now officially dangerous. You can:
- Create and destroy windows with impunity
- Split your terminal like a magician
- Navigate without touching your mouse
- Detach and reattach like a terminal ninja

You're ready for the advanced stuff. But remember: with great power comes great responsibility. And by responsibility, I mean dozens of forgotten sessions running on various servers that you'll discover months later.

---

*Next: [Chapter 6 - Advanced Features and Customization](06-advanced-features.md)*

*In which we transform from multiplexer users to multiplexer wizards*