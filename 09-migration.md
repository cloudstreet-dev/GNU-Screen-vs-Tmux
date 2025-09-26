# Chapter 9: Migration Guide - Switching Sides

*In which we provide a judgment-free zone for multiplexer converts, defectors, and the chronically indecisive*

## The Five Stages of Multiplexer Migration

1. **Denial**: "I don't need to switch. My current multiplexer is fine."
2. **Anger**: "Why is the prefix key different?! Why doesn't anything work?!"
3. **Bargaining**: "Maybe I can make the new one work exactly like the old one..."
4. **Depression**: "I'll never be as productive as I was before."
5. **Acceptance**: "Actually, this feature is pretty nice."

You're probably at stage 2 or 3. This chapter will get you to 5.

## Migration Path 1: Screen to Tmux

### The Muscle Memory Rehabilitation Program

The biggest challenge isn't learning Tmux—it's unlearning Screen. Your fingers have been pressing Ctrl+A for years, possibly decades. Here's how to ease the transition:

#### Option 1: Make Tmux Pretend to Be Screen

```bash
# ~/.tmux.conf - The "I Can't Let Go" Configuration

# Change prefix to Screen's Ctrl+A
unbind C-b
set-option -g prefix C-a
bind-key C-a send-prefix

# Screen-like key bindings
bind-key C-a last-window     # Ctrl+A Ctrl+A for last window
bind-key a send-prefix       # Ctrl+A a sends literal Ctrl+A
bind-key C-c new-window      # Ctrl+A Ctrl+C for new window
bind-key C-d detach          # Ctrl+A Ctrl+D to detach
bind-key C-n next-window     # Ctrl+A Ctrl+N for next
bind-key C-p previous-window # Ctrl+A Ctrl+P for previous
bind-key '"' choose-window   # Ctrl+A " for window list
bind-key A command-prompt -I "#W" "rename-window '%%'"  # Ctrl+A A to rename
bind-key k confirm-before -p "kill-window #W? (y/n)" kill-window
bind-key S split-window -v   # Ctrl+A S for horizontal split
bind-key | split-window -h   # Ctrl+A | for vertical split
bind-key Tab select-pane -t :.+ # Ctrl+A Tab to cycle panes
bind-key X remove-pane       # Ctrl+A X to kill pane
bind-key Q break-pane        # Ctrl+A Q to break pane to window

# Screen-like status line (as much as possible)
set -g status-left '[#S] '
set -g status-right '%H:%M %d-%b-%y'
set -g window-status-format '#I:#W#F'
set -g window-status-current-format '#I:#W#F'

# Other Screen-like behaviors
set -g base-index 0          # Start windows at 0 like Screen
set -g history-limit 10000   # Reasonable scrollback
set -g display-time 3000     # Message display time

# Make it look familiar
set -g status-style bg=black,fg=white
set -g window-status-current-style bg=white,fg=black
```

#### Option 2: Gradual Transition

Week 1: Use the Screen compatibility config above
Week 2: Switch prefix to Ctrl+B, keep other Screen bindings
Week 3: Start using Tmux's native bindings for new features
Week 4: Gradually replace Screen bindings with Tmux defaults
Week 5: You're now a Tmux user

#### Option 3: Cold Turkey

Delete your .screenrc, embrace the pain, and emerge stronger. Not recommended unless you enjoy suffering.

### Command Translation Guide

Your Screen commands and their Tmux equivalents:

```bash
# Session Management
screen -S name          → tmux new -s name
screen -ls              → tmux ls
screen -r name          → tmux attach -t name
screen -d -r name       → tmux attach -d -t name
screen -x name          → tmux attach -t name
screen -D -r name       → tmux attach -d -t name

# Inside Sessions
Ctrl+A d                → Ctrl+B d          # Detach
Ctrl+A c                → Ctrl+B c          # New window
Ctrl+A n                → Ctrl+B n          # Next window
Ctrl+A p                → Ctrl+B p          # Previous window
Ctrl+A "                → Ctrl+B w          # List windows
Ctrl+A A                → Ctrl+B ,          # Rename window
Ctrl+A k                → Ctrl+B &          # Kill window
Ctrl+A S                → Ctrl+B "          # Split horizontal
Ctrl+A |                → Ctrl+B %          # Split vertical
Ctrl+A Tab              → Ctrl+B o          # Next pane
Ctrl+A X                → Ctrl+B x          # Kill pane
Ctrl+A [                → Ctrl+B [          # Copy mode (same!)
Ctrl+A ]                → Ctrl+B ]          # Paste (same!)
Ctrl+A :                → Ctrl+B :          # Command mode (same!)
```

### Feature Migration Guide

#### Screen's Multiuser Mode → Tmux's Shared Sessions

Screen:
```bash
multiuser on
acladd username
```

Tmux (less sophisticated but works):
```bash
# Both users SSH to same server
# User 1:
tmux new -s shared

# User 2:
tmux attach -t shared

# Or use tmate for easier sharing
```

#### Screen's Logging → Tmux's Pipe-Pane

Screen:
```bash
Ctrl+A H  # Toggle logging
deflog on
```

Tmux:
```bash
# Start logging current pane
Ctrl+B : pipe-pane -o "cat >>~/tmux.log"

# Or in config for automatic logging
bind-key H pipe-pane -o "cat >>~/tmux-#W.log" \; display-message "Toggled logging to ~/tmux-#W.log"
```

#### Screen's Monitoring → Tmux's Activity Monitoring

Screen:
```bash
Ctrl+A M  # Monitor for activity
Ctrl+A _  # Monitor for silence
```

Tmux:
```bash
# In ~/.tmux.conf
setw -g monitor-activity on
set -g visual-activity on
setw -g monitor-silence 30  # Monitor 30 seconds of silence
```

### What You'll Miss from Screen

Let's be honest about what you're giving up:

1. **True multi-user support with ACLs**: Tmux's shared sessions are all-or-nothing
2. **Simpler configuration syntax**: (Just kidding, Screen's config is incomprehensible)
3. **Lighter resource usage**: Tmux uses a few more MB of RAM
4. **Universal availability**: Some ancient systems only have Screen
5. **Serial port support**: Screen can connect to serial devices directly

### What You'll Gain with Tmux

The rewards for your migration efforts:

1. **Readable configuration files**: You can actually understand what you wrote
2. **Better pane management**: Zooming, layouts, easy navigation
3. **Active development**: Features added this decade
4. **Plugin ecosystem**: Extend functionality without recompiling
5. **Better scripting**: Cleaner command syntax
6. **Modern terminal support**: True colors, better mouse support
7. **Session resurrection**: Save and restore your entire setup

## Migration Path 2: Tmux to Screen

First, let's address why you might be doing this:
- You've joined a team that uses Screen religiously
- You're working on ancient systems where Tmux isn't available
- You've decided that features are overrated
- You lost a bet

### The Simplification Process

Moving from Tmux to Screen is like moving from a smartphone back to a flip phone. Everything still works, just with fewer features and more button pressing.

#### Making Screen Feel Modern (Sort Of)

```bash
# ~/.screenrc - The "Missing Tmux" Configuration

# Startup
startup_message off
defscrollback 50000
term screen-256color

# Try to emulate Tmux status bar
hardstatus off
hardstatus alwayslastline
hardstatus string '%{= kG}[ %{G}%H %{g}][%= %{= kw}%?%-Lw%?%{r}(%{W}%n*%f%t%?(%u)%?%{r})%{w}%?%+Lw%?%?%= %{g}][%{B} %Y-%m-%d %{W} %c %{g}]'

# Tmux-like bindings (as much as possible)
# Change prefix to Ctrl+B (brave choice)
escape ^Bb

# Window management
bind c screen 1         # Start at window 1
bind , title           # Rename window
bind w windowlist -b   # Window list
bind s split          # Split (only horizontal though)
bind x remove         # Remove region
bind z only           # "Zoom" (remove other regions)

# Navigation
bind n next
bind p prev
bind 0 select 0
bind 1 select 1
bind 2 select 2
bind 3 select 3
bind 4 select 4
bind 5 select 5
bind 6 select 6
bind 7 select 7
bind 8 select 8
bind 9 select 9

# Status commands
bind ? help
bind r source ~/.screenrc
bind d detach

# Colors (trying our best)
attrcolor b ".I"
termcapinfo xterm 'Co#256:AB=\E[48;5;%dm:AF=\E[38;5;%dm'
defbce on
```

### Feature Downgrade Guide

#### Tmux's Pane Management → Screen's Regions

Tmux:
```bash
Ctrl+B %  # Vertical split
Ctrl+B "  # Horizontal split
Ctrl+B z  # Zoom pane
Ctrl+B arrow  # Navigate panes
```

Screen (limited):
```bash
Ctrl+A S      # Horizontal split only
Ctrl+A |      # Vertical (v4.1+ only)
Ctrl+A Q      # Remove all regions except current (poor man's zoom)
Ctrl+A Tab    # Cycle regions (no directional navigation)
```

#### Tmux's Scripting → Screen's... Scripting?

Tmux:
```bash
tmux send-keys -t session:window "command" Enter
tmux new-window -n name 'command'
```

Screen:
```bash
screen -S session -X stuff "command\n"
screen -S session -X screen -t name sh -c 'command'
```

It works, but you'll miss Tmux's clarity.

#### Tmux's Plugins → Screen's Patience

There's no plugin system for Screen. You'll need to:
1. Find patches
2. Apply them to source
3. Recompile
4. Hope they work
5. Repeat when updating

Or just accept Screen as it is.

### What You'll Miss from Tmux

1. **Sane configuration syntax**: Get ready for hieroglyphics
2. **Proper pane support**: Regions are not panes
3. **Active development**: Bug fixes come in geological time
4. **Plugins**: What plugins?
5. **Modern features**: Mouse support, true colors, etc.
6. **Good documentation**: Man pages from 1987 await

### What You'll Gain with Screen

1. **Simplicity**: Fewer features = fewer things to break
2. **Ubiquity**: It's probably already installed
3. **Stability**: Code that hasn't changed in years can't introduce new bugs
4. **Lower resource usage**: Saves literally dozens of kilobytes
5. **Veteran status**: "I use Screen" implies decades of experience

## The Hybrid Approach: Using Both

Why choose when you can have both? Many professionals use both Screen and Tmux depending on the situation.

### Unified Configuration Approach

Create aliases that work with whatever's available:

```bash
# ~/.bashrc or ~/.zshrc

# Detect which multiplexer is available
if command -v tmux &> /dev/null; then
    export MULTIPLEXER="tmux"
elif command -v screen &> /dev/null; then
    export MULTIPLEXER="screen"
fi

# Universal aliases
mux() {
    case "$MULTIPLEXER" in
        tmux)
            tmux new-session -s "$1" 2>/dev/null || tmux attach-session -t "$1"
            ;;
        screen)
            screen -R "$1"
            ;;
        *)
            echo "No terminal multiplexer found!"
            ;;
    esac
}

muxls() {
    case "$MULTIPLEXER" in
        tmux)
            tmux list-sessions 2>/dev/null || echo "No tmux sessions"
            ;;
        screen)
            screen -ls
            ;;
    esac
}

muxkill() {
    case "$MULTIPLEXER" in
        tmux)
            tmux kill-session -t "$1"
            ;;
        screen)
            screen -S "$1" -X quit
            ;;
    esac
}

# Quick attach to last session
alias ma='[ "$MULTIPLEXER" = "tmux" ] && tmux attach || screen -r'
```

### When to Use Which

**Use Screen when:**
- Working on legacy systems
- You need multi-user support with permissions
- Resources are extremely limited
- The system admin refuses to install Tmux
- You want to impress old-school Unix users

**Use Tmux when:**
- Setting up new environments
- You need advanced layouts
- Working on modern systems
- You want plugins and customization
- Collaborating with developers under 40

## Migration Checklist

### Pre-Migration
- [ ] Backup your current configuration
- [ ] Document your most-used commands
- [ ] Warn your team about temporary productivity loss
- [ ] Schedule migration during low-pressure period
- [ ] Prepare stress ball

### During Migration
- [ ] Install the new multiplexer
- [ ] Create compatibility configuration
- [ ] Practice basic commands
- [ ] Migrate one workflow at a time
- [ ] Keep cheat sheet visible

### Post-Migration
- [ ] Gradually adopt native keybindings
- [ ] Explore new features
- [ ] Update documentation
- [ ] Help others migrate
- [ ] Never look back (or do, we don't judge)

## Common Migration Pitfalls

### The "Make It Exactly the Same" Trap
Don't spend months making Tmux work exactly like Screen (or vice versa). Embrace the differences.

### The "All or Nothing" Mistake
You don't have to migrate everything at once. Start with new projects.

### The "Config Perfectionism" Disease
Your configuration will never be perfect. Ship it and iterate.

### The "Evangelism" Phase
Yes, your new multiplexer is great. No, not everyone needs to hear about it every day.

## Success Stories

### "From Screen to Tmux in 30 Days"
*"I thought I'd never leave Screen after 15 years. Then I discovered Tmux's pane zooming. Never looked back."* - Anonymous DevOps Engineer

### "Why I Went Back to Screen"
*"Tmux is great, but when you're managing 200 legacy servers that only have Screen, you learn to love simplicity."* - Grumpy SysAdmin

### "I Use Both and I'm Not Sorry"
*"Tmux for development, Screen for production servers. Best of both worlds."* - Pragmatic Developer

## Conclusion: It's Just a Tool

At the end of the day, both Screen and Tmux are just tools. The best multiplexer is the one that:
1. You know how to use
2. Solves your problems
3. Is available when you need it

Migration isn't betrayal—it's growth. Whether you're moving to Tmux for the features or to Screen for the simplicity, you're expanding your toolkit.

Remember: Real professionals can use both. And complain about both. Often simultaneously.

---

*Next: [Chapter 10 - Conclusion: Choosing Your Champion](10-conclusion.md)*

*In which we pretend there's a right answer while acknowledging there isn't*