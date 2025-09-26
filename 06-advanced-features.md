# Chapter 6: Advanced Features and Customization

*In which we descend into the configuration rabbit hole and emerge as terminal multiplexer wizards with questionably elaborate setups*

## The Configuration Files: Your Digital DNA

Configuration files are where terminal multiplexers transform from tools into *your* tools. It's like the difference between a rental car and your own car - sure, they both drive, but only one has your preset radio stations and that weird smell you've grown to love.

### Screen: The .screenrc Archaeology

Screen's configuration syntax looks like it was designed by someone who really loved brevity and really hated documentation.

```bash
# ~/.screenrc - A glimpse into madness

# The basics everyone needs
startup_message off                # We know we're in Screen, thanks
defscrollback 10000               # Because 100 lines is insulting
vbell off                         # Silence the beep of doom

# The status line that looks like regex had a baby with ASCII art
hardstatus alwayslastline
hardstatus string '%{= kG}[ %{G}%H %{g}][%= %{=kw}%?%-Lw%?%{r}(%{W}%n*%f%t%?(%u)%?%{r})%{w}%?%+Lw%?%%= %{g}][%{B}%Y-%m-%d %{W}%c %{g}]'

# What does that mean? Even Screen doesn't know
# %{= kG}  - Color codes that look like modem line noise
# %H       - Hostname (at least this makes sense)
# %-Lw     - Windows to the left (L = left, w = windows, - = ???)
# %n       - Window number (n for number, revolutionary!)
```

### Tmux: The .tmux.conf Renaissance

Tmux configuration reads like actual configuration, not encrypted messages from aliens:

```bash
# ~/.tmux.conf - Configuration for humans

# Set prefix to Ctrl+Space (easier than Ctrl+B)
unbind C-b
set-option -g prefix C-Space
bind-key C-Space send-prefix

# Start windows and panes at 1, not 0 (we're not programmers here!)
set -g base-index 1
setw -g pane-base-index 1

# Renumber windows when one closes
set -g renumber-windows on

# Status bar that doesn't require a PhD to understand
set -g status-style bg=black,fg=white
set -g status-left '#[bg=blue,fg=black] #S #[bg=black] '
set -g status-right '#[bg=yellow,fg=black] %H:%M #[bg=green,fg=black] %Y-%m-%d '
```

## Advanced Screen Features

### Screen's Multiuser Mode (Collaborative Suffering)

Screen allows multiple users to attach to the same session, perfect for pair programming or showing someone exactly where they screwed up:

```bash
# In ~/.screenrc
multiuser on

# Start Screen
$ screen -S shared-session

# Inside Screen, add users
Ctrl+A :acladd colleague_username

# Your colleague connects
$ screen -x your_username/shared-session

# Control permissions
Ctrl+A :aclchg colleague_username -w "#"  # Remove write access to window 0
Ctrl+A :aclchg colleague_username +x detach  # Allow detaching
```

It's like Google Docs, but for terminals, and with more opportunity for chaos.

### Screen's Regions and Layouts

Screen can save and restore layout configurations:

```bash
# Create your perfect layout
Ctrl+A S      # Split horizontally
Ctrl+A Tab    # Move to new region
Ctrl+A c      # Create window
Ctrl+A |      # Split vertically

# Save this masterpiece
Ctrl+A :layout save default

# Later, after you've destroyed everything
Ctrl+A :layout load default

# Cycle through saved layouts
Ctrl+A :layout next
Ctrl+A :layout prev
```

### Screen's Bindkey Magic

Remap keys to your heart's content:

```bash
# ~/.screenrc

# Function key bindings
bindkey -k k1 select 0   # F1 goes to window 0
bindkey -k k2 select 1   # F2 goes to window 1
bindkey -k k3 select 2   # F3 goes to window 2

# Custom commands
bind R eval "source ~/.screenrc" "echo '.screenrc reloaded!'"
bind s split              # Lowercase s for split
bind v split -v          # v for vertical (if supported)
bind x remove            # x to close region
```

### Screen's Window Monitoring

Screen can notify you when something happens in a background window:

```bash
# Monitor for activity
Ctrl+A M  # Toggle monitoring on current window

# Monitor for silence (30 seconds)
Ctrl+A _

# In ~/.screenrc for all windows
defmonitor on
activity "Activity in window %n"  # Custom message

# For specific windows
screen -t logs 1 tail -f /var/log/syslog
monitor on                        # This window is now watched
```

## Advanced Tmux Features

### Tmux's Hooks (Event-Driven Greatness)

Tmux can run commands when certain events occur:

```bash
# ~/.tmux.conf

# Alert when a window has activity
set-hook -g alert-activity 'run-shell "notify-send \"Activity in Tmux\""'

# Log when sessions are created
set-hook -g session-created 'run-shell "echo \"Session created: #{session_name}\" >> ~/tmux.log"'

# Automatically rename windows based on current command
set-hook -g after-new-window 'automatic-rename on'

# Save session state periodically
set-hook -g after-resize-pane 'run-shell "tmux list-sessions -F \"#{session_name}\" > ~/.tmux-sessions"'
```

### Tmux's Display Menus

Tmux 3.0+ has actual menus! Welcome to the 1990s!

```bash
# Create a custom menu
bind-key m display-menu -T "Quick Actions" \
    "Htop" h "new-window htop" \
    "Logs" l "new-window 'tail -f /var/log/syslog'" \
    "Git Status" g "new-window 'git status'" \
    "-" \
    "Reload Config" r "source-file ~/.tmux.conf"
```

### Tmux's Conditional Formatting

Make your status bar react to conditions:

```bash
# Change color based on prefix key press
set -g status-left '#{?client_prefix,#[bg=red],#[bg=green]}[#S] '

# Show different info based on window activity
setw -g window-status-format '#{?window_activity,#[fg=red],#[fg=white]}#I:#W'

# Indicate zoomed panes
setw -g window-status-current-format '#{?window_zoomed_flag,🔍,}#I:#W'
```

### Tmux's Command Aliases

Create shortcuts for complex commands:

```bash
# ~/.tmux.conf

# Quick splits with better keys
bind | split-window -h -c "#{pane_current_path}"
bind - split-window -v -c "#{pane_current_path}"

# Session management shortcuts
bind C new-session
bind L switch-client -l  # Last session

# Window swapping made easy
bind -r "<" swap-window -t -1
bind -r ">" swap-window -t +1

# Pane resizing for humans
bind -r H resize-pane -L 5
bind -r J resize-pane -D 5
bind -r K resize-pane -U 5
bind -r L resize-pane -R 5
```

## Power User Configurations

### The Ultimate Screen Configuration

```bash
# ~/.screenrc - The works

# Basic sanity
startup_message off
defscrollback 50000
term screen-256color
altscreen on

# Better keyboard shortcuts
escape ^Zz  # Change prefix to Ctrl+Z
bind c screen 1  # Windows start at 1
bind ^c screen 1
bind 0 select 10  # Make 0 go to window 10

# Window management
bind = resize =
bind + resize +1
bind - resize -1
bind _ resize max

# Logging
deflog on
logfile $HOME/logs/screen-%Y%m%d-%n.log

# Status lines (yes, plural)
caption always "%{= kw}%-w%{= BW}%n %t%{-}%+w %-= %{= kG} %l %{= kM}%c"
hardstatus alwayslastline
hardstatus string '%{= kG}[ %{G}%H %{g}][ %{=kw}%?%-Lw%?%{r}(%{W}%n*%f%t%?(%u)%?%{r})%{w}%?%+Lw%?%?%= %{g}][%{B} %Y-%m-%d %{W}%c %{g}]'

# Multiuser setup
multiuser on
acladd root  # Living dangerously

# Custom layouts
layout autosave on
layout new main
select 0
layout new coding
select 1
split
focus down
select 2
layout select main

# SSH agent fix
setenv SSH_AUTH_SOCK $HOME/.ssh/ssh_auth_sock
```

### The Ultimate Tmux Configuration

```bash
# ~/.tmux.conf - Maximum power

# Better prefix
set -g prefix C-a
unbind C-b
bind C-a send-prefix
bind a send-prefix  # For nested tmux

# General settings
set -g default-terminal "screen-256color"
set -ga terminal-overrides ",xterm-256color:Tc"
set -g history-limit 50000
set -g buffer-limit 20
set -sg escape-time 0
set -g display-time 1500
set -g remain-on-exit off
set -g repeat-time 300
setw -g allow-rename off
setw -g automatic-rename off
setw -g aggressive-resize on

# Start at 1
set -g base-index 1
setw -g pane-base-index 1
set -g renumber-windows on

# Mouse support
set -g mouse on

# Status bar
set -g status on
set -g status-interval 5
set -g status-position bottom
set -g status-justify left
set -g status-style "fg=white,bg=black"

# Left side
set -g status-left-length 60
set -g status-left "#[fg=black,bg=blue,bold] #S #[fg=blue,bg=black,nobold]"

# Right side
set -g status-right-length 60
set -g status-right "#[fg=brightblack,bg=black]#[fg=white,bg=brightblack] %Y-%m-%d #[fg=white,bg=brightblack]#[fg=white,bg=brightblack,bold] %H:%M "

# Window status
setw -g window-status-format "#[fg=white,bg=black] #I #W "
setw -g window-status-current-format "#[fg=black,bg=cyan]#[fg=black,bg=cyan] #I #W #[fg=cyan,bg=black]"
setw -g window-status-separator ""

# Pane borders
set -g pane-active-border-style "fg=blue"
set -g pane-border-style "fg=brightblack"

# Better bindings
bind r source-file ~/.tmux.conf \; display "Config reloaded!"
bind | split-window -h -c "#{pane_current_path}"
bind - split-window -v -c "#{pane_current_path}"
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R
bind -r H resize-pane -L 5
bind -r J resize-pane -D 5
bind -r K resize-pane -U 5
bind -r L resize-pane -R 5
bind Tab last-window
bind C-p previous-window
bind C-n next-window
bind -r "<" swap-window -t -1
bind -r ">" swap-window -t +1
bind X kill-session
bind x kill-pane
bind q display-panes
bind c new-window -c "#{pane_current_path}"

# Copy mode
setw -g mode-keys vi
bind Enter copy-mode
bind -T copy-mode-vi v send -X begin-selection
bind -T copy-mode-vi y send -X copy-selection-and-cancel
bind -T copy-mode-vi Escape send -X cancel
bind p paste-buffer

# Plugins (using TPM)
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'tmux-plugins/tmux-resurrect'
set -g @plugin 'tmux-plugins/tmux-continuum'
set -g @plugin 'tmux-plugins/tmux-yank'
set -g @plugin 'tmux-plugins/tmux-prefix-highlight'
set -g @plugin 'tmux-plugins/tmux-pain-control'

# Plugin settings
set -g @resurrect-capture-pane-contents 'on'
set -g @continuum-restore 'on'
set -g @continuum-save-interval '5'

# Initialize TPM
run '~/.tmux/plugins/tpm/tpm'
```

## Scripting and Automation

### Screen Automation

```bash
#!/bin/bash
# screen-dev.sh - Automated development environment

screen -dmS dev
screen -S dev -X screen -t editor 0 vim
screen -S dev -X screen -t server 1 npm run dev
screen -S dev -X screen -t database 2 mysql
screen -S dev -X screen -t git 3 bash
screen -S dev -X select 0
screen -r dev
```

### Tmux Automation

```bash
#!/bin/bash
# tmux-dev.sh - Sophisticated development environment

tmux new-session -d -s dev -n editor
tmux send-keys -t dev:editor 'vim .' Enter

tmux new-window -t dev -n server
tmux send-keys -t dev:server 'npm run dev' Enter

tmux new-window -t dev -n database
tmux split-window -t dev:database -h
tmux send-keys -t dev:database.0 'mysql' Enter
tmux send-keys -t dev:database.1 'redis-cli' Enter

tmux new-window -t dev -n git
tmux send-keys -t dev:git 'git status' Enter

tmux select-window -t dev:editor
tmux attach-session -t dev
```

## Integration with Other Tools

### Vim Integration

```vim
" ~/.vimrc
" Tmux integration
if exists('$TMUX')
    " Fix cursor shape in tmux
    let &t_SI = "\<Esc>Ptmux;\<Esc>\e[5 q\<Esc>\\"
    let &t_EI = "\<Esc>Ptmux;\<Esc>\e[2 q\<Esc>\\"

    " Navigate seamlessly between vim and tmux panes
    nnoremap <C-h> :TmuxNavigateLeft<CR>
    nnoremap <C-j> :TmuxNavigateDown<CR>
    nnoremap <C-k> :TmuxNavigateUp<CR>
    nnoremap <C-l> :TmuxNavigateRight<CR>
endif
```

### Shell Integration

```bash
# ~/.bashrc or ~/.zshrc

# Auto-start tmux
if command -v tmux &> /dev/null && [ -n "$PS1" ] && [[ ! "$TERM" =~ screen ]] && [[ ! "$TERM" =~ tmux ]] && [ -z "$TMUX" ]; then
    exec tmux new-session -A -s main
fi

# Tmux-specific aliases
alias ta='tmux attach -t'
alias tad='tmux attach -d -t'
alias ts='tmux new-session -s'
alias tl='tmux list-sessions'
alias tksv='tmux kill-server'
alias tkss='tmux kill-session -t'

# Screen-specific aliases
alias sls='screen -ls'
alias sr='screen -r'
alias sS='screen -S'
```

## Performance Tuning

### Screen Performance

```bash
# ~/.screenrc
# Reduce overhead
msgwait 0
msgminwait 0
defnonblock 5

# Optimize for slow connections
defc1 off
defautonuke off

# Better scrolling
termcapinfo xterm* ti@:te@
```

### Tmux Performance

```bash
# ~/.tmux.conf
# Reduce escape time for faster vim response
set -sg escape-time 0

# Increase scrollback for heavy output
set -g history-limit 100000

# Optimize for remote connections
set -g assume-paste-time 0
set -g focus-events off
```

## Troubleshooting Common Issues

### The "256 Colors Not Working" Blues

```bash
# Screen fix
# ~/.screenrc
term screen-256color
attrcolor b ".I"
termcapinfo xterm 'Co#256:AB=\E[48;5;%dm:AF=\E[38;5;%dm'

# Tmux fix
# ~/.tmux.conf
set -g default-terminal "screen-256color"
set -ga terminal-overrides ",*256col*:Tc"
```

### The "My Keys Are Weird" Situation

```bash
# Usually a TERM issue
export TERM=xterm-256color  # Before starting multiplexer

# Or inside the multiplexer
set -g default-terminal "screen-256color"  # Tmux
term screen-256color  # Screen
```

## Conclusion: You're Now Dangerous

With these advanced features, you've transcended mere usage. You're now:
- Writing configurations that would make the original developers proud (or concerned)
- Automating everything because typing is for mortals
- Integrating with tools they haven't even heard of
- Creating setups so complex you need documentation for your documentation

Remember: Just because you *can* configure something doesn't mean you *should*. But where's the fun in restraint?

---

*Next: [Chapter 7 - The Great Comparison](07-comparison.md)*

*In which we finally settle the debate (spoiler: we don't)*