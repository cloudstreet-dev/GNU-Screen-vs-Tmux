# Chapter 3: Tmux - The Modern Contender

*In which the new kid on the block shows up with a leather jacket and better documentation*

## The Origin Story

In 2007, while Screen was celebrating its 20th birthday and thinking about retirement, Nicholas Marriott was getting frustrated. He wanted a terminal multiplexer that was actively maintained, had cleaner code, and didn't require a PhD in archaeology to understand the source. So he did what any reasonable programmer would do: he wrote his own.

Tmux (Terminal Multiplexer) burst onto the scene like a Silicon Valley startup - full of promises, modern ideas, and an suspicious amount of documentation. It looked at Screen's decades of legacy and said, "That's cute, but what if we did it *right* this time?"

## The Tmux Philosophy

If Screen is the reliable grandfather, Tmux is the overachieving millennial cousin who:
- Documents everything
- Has a consistent command structure
- Actually makes sense (mostly)
- Updates regularly (what a concept!)
- Has a configuration format that humans can read

Tmux's core principles:
1. Consistency is king
2. Everything should be scriptable
3. Modern terminals deserve modern features
4. Documentation shouldn't require a decoder ring
5. BSD license > GPL (shots fired!)

## The Architecture: Sessions, Windows, and Panes, Oh My!

Tmux introduces a clear hierarchy that actually makes sense:

```
Server (runs in background)
  └── Session (your project)
      └── Window (your workspace)
          └── Pane (your actual terminal)
```

It's like Russian nesting dolls, but useful. Each level has a purpose:
- **Server**: The silent guardian that keeps everything running
- **Session**: A collection of windows for a specific task/project
- **Window**: Like browser tabs, but for terminals
- **Pane**: Split windows for when one terminal isn't enough

## Your First Tmux Experience

Starting Tmux is refreshingly simple:

```bash
$ tmux
```

You're now in Tmux! It even has a status bar at the bottom so you know you're in Tmux. Screen users are already jealous.

### The Sacred Ctrl+B (Because Ctrl+A Was Taken)

Tmux's prefix key is `Ctrl+B`. Why B? Because:
- A was taken by Screen
- B comes after A
- It's still kind of accessible with one hand
- They had to pick something

Yes, it's slightly more awkward than Ctrl+A, but Tmux users will tell you it's a small price to pay for modernity.

## Essential Tmux Commands

Here are the commands that'll make you productive:

### Session Management
- `tmux new -s name` - Create a named session (look ma, readable commands!)
- `tmux ls` - List sessions (shorter than screen -ls!)
- `tmux attach -t name` - Attach to a session
- `Ctrl+B d` - Detach from session
- `Ctrl+B $` - Rename session

### Window Management
- `Ctrl+B c` - Create window
- `Ctrl+B n` - Next window
- `Ctrl+B p` - Previous window
- `Ctrl+B ,` - Rename window
- `Ctrl+B &` - Kill window (with confirmation, because Tmux cares)
- `Ctrl+B w` - List windows (interactive!)

### Pane Management (Tmux's Party Trick)
- `Ctrl+B %` - Split vertically
- `Ctrl+B "` - Split horizontally
- `Ctrl+B arrow` - Navigate panes
- `Ctrl+B z` - Zoom pane (full screen that pane!)
- `Ctrl+B x` - Kill pane
- `Ctrl+B {` / `Ctrl+B }` - Swap panes

### The Magic of Copy Mode
- `Ctrl+B [` - Enter copy mode
- Navigate with arrow keys or vim bindings
- `Space` - Start selection
- `Enter` - Copy selection
- `Ctrl+B ]` - Paste

## Configuration: The ~/.tmux.conf Revolution

Tmux's configuration file is `~/.tmux.conf`, and it's actually readable by humans! Here's a configuration that'll make you feel like a terminal wizard:

```bash
# Tmux configuration that doesn't require a manual

# Improve colors
set -g default-terminal "screen-256color"

# Set scrollback buffer to 10000
set -g history-limit 10000

# Customize the status line
set -g status-fg  green
set -g status-bg  black
set -g status-left '#[fg=cyan]#S #[fg=white]|'
set -g status-right '#[fg=yellow]#(uptime | cut -d "," -f 3-) #[fg=white]| #[fg=cyan]%H:%M '

# Start window numbering at 1 (0 is too far from 1)
set -g base-index 1
set -g pane-base-index 1

# Renumber windows when one is closed
set -g renumber-windows on

# Mouse support - because sometimes you're lazy
set -g mouse on

# Vim-style pane selection
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R

# Easy config reload
bind r source-file ~/.tmux.conf \; display-message "Config reloaded!"

# Split panes using | and - (makes more sense)
bind | split-window -h
bind - split-window -v
unbind '"'
unbind %

# Don't rename windows automatically
set-option -g allow-rename off

# Loud or quiet?
set -g visual-activity off
set -g visual-bell off
set -g visual-silence off
set -g bell-action none

# Pane borders
set -g pane-border-style 'fg=colour235'
set -g pane-active-border-style 'fg=colour51'

# Status bar styling
set -g status-position bottom
set -g status-justify left
set -g status-style 'bg=colour234 fg=colour137'
set -g status-left-length 50
set -g status-right-length 50

# Message styling
set -g message-style 'fg=colour232 bg=colour166 bold'
```

Look at that! You can actually understand what each line does without consulting ancient scrolls!

## Advanced Tmux Features

### Synchronized Panes (Type Once, Execute Everywhere)

Perfect for managing multiple servers:

```bash
# Turn on synchronization
Ctrl+B :
setw synchronize-panes on

# Now everything you type goes to all panes!
# Turn it off before you accidentally rm -rf / somewhere
```

### Session Groups (Shared Sessions with Independent Views)

```bash
# Create a new session in a group
tmux new-session -s original
tmux new-session -t original -s shared

# Now both sessions share windows but can view different ones
```

### Tmux Resurrect (Because Even Tmux Can Die)

With the Tmux Plugin Manager (TPM), you can save and restore sessions:

```bash
# Install TPM first
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm

# Add to ~/.tmux.conf
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-resurrect'
set -g @plugin 'tmux-plugins/tmux-continuum'

# Initialize TPM (add to bottom of ~/.tmux.conf)
run '~/.tmux/plugins/tpm/tpm'

# Now you can:
# Ctrl+B Ctrl+S - save
# Ctrl+B Ctrl+R - restore
```

### Tmux Scripting (Automation for the Win)

Tmux is scriptable! Create a development environment with one command:

```bash
#!/bin/bash
# dev-setup.sh

tmux new-session -d -s dev
tmux rename-window -t dev:0 'editor'
tmux send-keys -t dev:0 'vim .' Enter

tmux new-window -t dev:1 -n 'server'
tmux send-keys -t dev:1 'npm run dev' Enter

tmux new-window -t dev:2 -n 'git'
tmux send-keys -t dev:2 'git status' Enter

tmux select-window -t dev:0
tmux attach-session -t dev
```

## Tmux's Modern Conveniences

### The Status Bar That Actually Helps

Unlike Screen's cryptic status line, Tmux's status bar is:
- Readable by humans
- Customizable without learning hieroglyphics
- Actually useful

Default information includes:
- Session name
- Window list
- Current window highlighted
- Time (because why not)
- System info (if you configure it)

### Integration with Modern Tools

Tmux plays nice with:
- Vim (vim-tmux-navigator for seamless navigation)
- Git (show branch in status)
- System monitors (CPU, memory in status bar)
- Your shell (proper color support!)
- The clipboard (finally!)

### Better Mouse Support

Yes, you can use your mouse in Tmux:
- Click to select panes
- Drag to resize panes
- Scroll to... scroll
- Right-click for context menus (in some terminals)

The hardcore terminal users are scandalized, but sometimes reaching for the mouse is just easier.

## Tmux Workflows

### The Developer Setup

```bash
# Create a session for your project
tmux new -s myproject

# Split into panes
# Editor on the left (70% width)
# Terminal on top right
# Server logs on bottom right
Ctrl+B %  # Split vertically
Ctrl+B "  # Split horizontally on the right

# Resize panes to taste
Ctrl+B Alt+Arrow keys
```

### The System Administrator's Dashboard

```bash
# Window 0: Multiple servers via SSH
# - Create 4 panes
# - SSH to different servers
# - Synchronize panes for mass commands

# Window 1: Monitoring
# - htop in one pane
# - Log files in others

# Window 2: Documentation/Notes
# - Man pages
# - Your runbook
```

### The "I'm Presenting" Mode

```bash
# Increase font size in your terminal
# Create a simple layout
tmux new -s demo

# Use zoom (Ctrl+B z) to focus on one pane
# Clear screen often
# Keep it simple
```

## Tmux Plugins: There's a Plugin for That

The Tmux plugin ecosystem is thriving:

- **tmux-resurrect**: Save/restore sessions
- **tmux-continuum**: Automatic session saves
- **tmux-sensible**: Sensible defaults everyone can agree on
- **tmux-pain-control**: Better pane management
- **tmux-copycat**: Enhanced searching
- **tmux-yank**: Better copy/paste
- **tmux-battery**: Battery status in status bar
- **tmux-cpu**: CPU usage in status bar
- **tmux-online-status**: Are you online? Now you know!

It's like the App Store, but for your terminal multiplexer.

## Tmux Gotchas and Solutions

### The Nested Tmux Problem

When you SSH into a server and run Tmux inside Tmux:
```bash
# Send prefix to inner tmux
Ctrl+B Ctrl+B <command>

# Or change the inner tmux's prefix
set -g prefix C-a  # In inner tmux config
```

### The "My Colors Look Weird" Issue

```bash
# In ~/.tmux.conf
set -g default-terminal "screen-256color"
set -ga terminal-overrides ",*256col*:Tc"

# Or if that doesn't work
export TERM=xterm-256color  # Before starting tmux
```

### The Clipboard Conundrum

```bash
# On macOS, install reattach-to-user-namespace
brew install reattach-to-user-namespace

# Add to ~/.tmux.conf
set-option -g default-command "reattach-to-user-namespace -l $SHELL"
```

## The Tmux Community

The Tmux community is like the cool kids' table at the terminal cafeteria. They're:
- Active on GitHub
- Actually responsive to issues
- Creating new plugins weekly
- Writing blog posts with titles like "10 Tmux Tips That Will Change Your Life"
- Converting Screen users one at a time

Common phrases in the Tmux community:
- "Have you tried the latest plugin?"
- "Check my dotfiles repo"
- "I mapped that to a better key"
- "Screen? People still use that?"
- "Let me share my configuration" (proceeds to share 500 lines)

## Performance and Resource Usage

Tmux is efficient, but it's not trying to run on your calculator:
- Uses marginally more memory than Screen (we're talking megabytes)
- CPU usage is negligible unless you're doing something weird
- Handles hundreds of panes without breaking a sweat
- Smooth scrolling and redrawing
- Actually uses your terminal's capabilities

## Tmux in the Cloud Era

Tmux has adapted well to modern development:

### Container Development
```bash
# Tmux + Docker
tmux new-window -n docker
tmux send-keys "docker-compose up" Enter
tmux split-window -h
tmux send-keys "docker logs -f app" Enter
```

### Remote Pair Programming
```bash
# Share a session (both users SSH to same server)
tmux new -s pair
chmod 777 /tmp/tmux-$(id -u)/default  # Allow other user
# Other user: tmux attach -t pair
```

### CI/CD Monitoring
```bash
# Create a monitoring dashboard
tmux new -s ci
# Split into panes for different pipelines
# Watch build logs in real-time
```

## Conclusion: The Modern Choice

Tmux is what happens when someone looks at a problem, understands the existing solution, and decides to do better. It's not revolutionary - it's evolutionary. It takes Screen's core concepts and polishes them until they shine.

Is it better than Screen? That depends on your definition of "better." If better means:
- More features
- Active development
- Better documentation
- Cleaner configuration
- Larger plugin ecosystem
- Modern terminal support

Then yes, Tmux is objectively better. But if better means "has been working fine since 1987," then Screen might still be your jam.

---

*Next: [Chapter 4 - Installation and Setup](04-installation.md)*

*In which we actually get these tools running on your machine (spoiler: it's easier than you think)*