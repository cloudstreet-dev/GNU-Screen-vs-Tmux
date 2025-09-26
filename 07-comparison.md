# Chapter 7: The Great Comparison

*In which we attempt to objectively compare two tools while our biases leak through like coffee through a cheap filter*

## The Tale of the Tape

Let's start with the boxing match statistics, because everything's more dramatic when presented like a fight:

| Feature | GNU Screen | Tmux | Winner |
|---------|------------|------|--------|
| **Age** | Born 1987 | Born 2007 | Screen (for wisdom) |
| **Lines of Code** | ~30,000 | ~60,000 | Screen (less is more?) |
| **Documentation** | Man page from 1987 | Actual readable docs | Tmux (by a mile) |
| **Default Config** | Spartan | Reasonable | Tmux |
| **Learning Curve** | Vertical cliff | Steep hill | Tmux |
| **Cool Factor** | Retro vintage | Modern hipster | Tie |

## Feature-by-Feature Breakdown

### Session Management

**Screen:**
- Sessions identified by PIDs or names
- Simple attach/detach model
- Multi-user support built-in
- Session sharing is straightforward
- No session groups

**Tmux:**
- Clean session naming
- Session groups for advanced workflows
- Better session info (windows, creation time)
- Scriptable session creation
- choose-tree for visual selection

**Winner: Tmux** - More features, better organization, actually makes sense.

### Window Management

**Screen:**
- Basic window operations
- Window monitoring (activity/silence)
- Simple window list
- Limited to 40 windows (0-39)

**Tmux:**
- Unlimited windows
- Window renaming that persists
- Better window navigation
- Window swapping and moving
- Visual window selection

**Winner: Tmux** - Unless you need exactly 40 windows, no more, no less.

### Pane Management

**Screen:**
- Horizontal splits (since forever)
- Vertical splits (since 2010, fashionably late)
- Basic navigation (Tab key)
- Limited resize options
- Regions don't persist across detach

**Tmux:**
- Splits in all directions from day one
- Sophisticated pane navigation
- Pane zooming
- Pane synchronization
- Layouts that actually save

**Winner: Tmux** - It's not even close. Screen's pane support feels like an afterthought.

### Copy Mode

**Screen:**
- Vim-like navigation
- Basic search
- Copy buffer
- Paste functionality
- That's about it

**Tmux:**
- Multiple copy modes (vim/emacs)
- Better search with highlighting
- Multiple paste buffers
- Integration with system clipboard (with plugins)
- Rectangle selection

**Winner: Tmux** - More options, better integration, actual features from this century.

### Status Line

**Screen's Status Line:**
```
[ hostname ][ 0$ bash  1$ vim  2-$ tail ][ 14:32 ]
```
Translation: Good luck figuring out what those symbols mean.

**Tmux's Status Line:**
```
[session-name] 0:bash 1:vim* 2:logs                   14:32 2024-01-15
```
Translation: You can actually read this.

**Winner: Tmux** - Human-readable by default. Revolutionary!

### Configuration

**Screen Configuration:**
```bash
# What does this do? Nobody knows!
hardstatus string '%{= kG}%-Lw%{= kW}%50> %n%f* %t%{= kG}%+Lw%<'
```

**Tmux Configuration:**
```bash
# Oh, it sets the status bar color to blue
set -g status-style bg=blue,fg=white
```

**Winner: Tmux** - Configuration you can read without a decoder ring.

### Performance

**Screen:**
- Minimal resource usage
- Runs on anything (including your calculator)
- Fast even on slow connections
- Handles thousands of lines of output without breaking a sweat

**Tmux:**
- Slightly higher resource usage (we're talking megabytes)
- Smooth scrolling
- Better handling of modern terminal features
- Can bog down with extreme pane counts

**Winner: Screen** - When you absolutely need to run on a potato.

### Scripting and Automation

**Screen:**
```bash
# Screen scripting
screen -S session -X screen -t window_name command
screen -S session -X stuff "commands\n"
```

**Tmux:**
```bash
# Tmux scripting
tmux new-window -n window_name 'command'
tmux send-keys -t session:window 'commands' Enter
```

**Winner: Tmux** - Cleaner syntax, better documentation, actually designed for scripting.

### Plugin Ecosystem

**Screen:**
- What plugins?
- Seriously, what plugins?
- Oh, you mean patches that require recompiling?

**Tmux:**
- TPM (Tmux Plugin Manager)
- Dozens of plugins
- Active community
- Easy installation

**Winner: Tmux** - Has an actual ecosystem vs. "edit the source code yourself."

## Use Case Showdown

### "I Need to SSH Into a Production Server"

**Screen:** Already installed, works immediately, no configuration needed.

**Tmux:** Might need to install it, but then you get nice features.

**Winner: Screen** - It's probably already there.

### "I'm Setting Up a Development Environment"

**Screen:** You can do it, but why would you want to?

**Tmux:** Built for this, with layouts, scripting, and automation.

**Winner: Tmux** - Modern development needs modern tools.

### "I Need to Collaborate with a Colleague"

**Screen:** Multi-user mode works well, if cryptic.

**Tmux:** Shared sessions work, but Screen's ACL system is more sophisticated.

**Winner: Screen** - One of the few things it does better.

### "I Want Pretty Colors and Customization"

**Screen:** 256 colors (if you configure it right), basic customization.

**Tmux:** True color support, extensive customization, actual themes.

**Winner: Tmux** - It's 2024, we deserve nice things.

### "I'm on a Really Old System"

**Screen:** Compiles with stone tools and fire.

**Tmux:** Needs libevent and modern(ish) libraries.

**Winner: Screen** - When your server is older than your junior developers.

## The Learning Curve Comparison

### Week 1
**Screen:** "How do I create a window? Ctrl+A c. Okay, got it."
**Tmux:** "How do I create a window? Ctrl+B c. Okay, got it."
**Tie**

### Week 2
**Screen:** "What does this configuration line do? No idea."
**Tmux:** "Oh, this configuration actually makes sense!"
**Winner: Tmux**

### Week 3
**Screen:** "How do I split vertically? Oh, I need version 4.1+?"
**Tmux:** "Splits, layouts, zooming... this is nice!"
**Winner: Tmux**

### Week 4
**Screen:** "I've learned everything Screen can do."
**Tmux:** "I'm still discovering features."
**Winner: Depends on your perspective**

## Community and Development

### GNU Screen
- Last major release: 2022 (4.9.0)
- Development pace: Geological
- Community: Small but devoted
- Documentation updates: What are those?
- Bug fixes: Eventually

### Tmux
- Regular releases
- Active development
- Large community
- Documentation updates with features
- Bug fixes: Actually happens

**Winner: Tmux** - Active development wins.

## Platform Support

### GNU Screen
- Linux: ✓ (everywhere)
- macOS: ✓ (but removed from base system)
- BSD: ✓ (naturally)
- Windows (Cygwin): ✓ (sort of)
- WSL: ✓
- Ancient Unix: ✓
- That weird embedded system: ✓

### Tmux
- Linux: ✓
- macOS: ✓
- BSD: ✓ (OpenBSD is home)
- Windows (Cygwin): ✓
- WSL: ✓
- Ancient Unix: ✗
- That weird embedded system: Maybe?

**Winner: Screen** - Runs on everything, including your smart fridge.

## The Philosophical Differences

### Screen's Philosophy
"We've been doing it this way since 1987, and we're not changing now. If it was good enough for your parents, it's good enough for you."

### Tmux's Philosophy
"Let's make a terminal multiplexer that people actually want to use, with features that make sense and documentation that humans can read."

## Common Complaints

### About Screen
- "The configuration syntax is insane"
- "Why doesn't vertical split work?"
- "The status line looks like line noise"
- "Where are the features?"
- "This documentation was written by sadists"

### About Tmux
- "Ctrl+B is harder to reach than Ctrl+A"
- "It's not installed by default"
- "Too many features I'll never use"
- "My muscle memory is all wrong"
- "It uses more resources" (barely)

## Migration Pain Points

### Screen to Tmux
- Relearning prefix key
- Different configuration syntax
- Missing multi-user ACLs
- Different copy mode behavior
- Having to install it everywhere

### Tmux to Screen
- Loss of features (many features)
- Cryptic configuration
- Limited pane support
- Worse status line
- Feeling like you've traveled back in time

## The Benchmark Tests

### Startup Time
```bash
$ time screen -c /dev/null -dmS test -X quit
real    0m0.012s

$ time tmux new -d -s test \; kill-session -t test
real    0m0.018s
```
**Winner: Screen** - By 6 milliseconds. Call the press!

### Memory Usage
```bash
# Screen
PID    VSZ    RSS
1234   7840   3052

# Tmux
PID    VSZ    RSS
5678   12460  4924
```
**Winner: Screen** - Uses less memory than a JavaScript "Hello World"

### Feature Count
- Screen: Dozens
- Tmux: Hundreds

**Winner: Tmux** - Unless you're a minimalist

## The Objective Verdict

Looking at pure numbers and features:

| Category | Screen | Tmux |
|----------|--------|------|
| Features | 3/10 | 9/10 |
| Ease of Use | 5/10 | 8/10 |
| Documentation | 3/10 | 8/10 |
| Performance | 9/10 | 8/10 |
| Compatibility | 10/10 | 7/10 |
| Customization | 5/10 | 9/10 |
| Community | 4/10 | 8/10 |
| Development | 3/10 | 9/10 |
| **Total** | **42/80** | **66/80** |

## The Subjective Verdict

**Choose Screen if:**
- It's already installed and you need it now
- You're working on ancient systems
- You need multi-user collaboration with ACLs
- You value stability over features
- You enjoy the vintage computing aesthetic
- Resource usage is critical
- You hate change

**Choose Tmux if:**
- You're setting up a new environment
- You want modern features
- You value good documentation
- You like customization
- You need advanced scripting
- You want an active ecosystem
- You appreciate good design

## The Real Verdict

Here's the truth: **both tools are fantastic**. The fact that we're comparing them at all means they've both succeeded in their mission. Screen has been reliably multiplexing terminals for longer than many developers have been alive. Tmux brought modern sensibilities to terminal multiplexing.

The "better" tool is the one that:
1. Is available on your system
2. Meets your needs
3. You know how to use
4. Your team uses

In practice, most power users end up knowing both, using whichever is available or appropriate for the situation.

## The Prediction

In 10 years:
- Screen will still be running on that server nobody wants to touch
- Tmux will have added features we haven't imagined yet
- Someone will create a "revolutionary" new multiplexer in Rust
- We'll still be having this debate
- Both will still be useful

---

*Next: [Chapter 8 - Real-World Scenarios and Use Cases](08-use-cases.md)*

*In which we stop arguing and start solving actual problems*