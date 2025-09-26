# Chapter 8: Real-World Scenarios and Use Cases

*In which we stop talking theory and start solving actual problems that keep developers up at night (besides caffeine)*

## Scenario 1: The Remote Server Administration Dance

You're managing 10 servers, and you need to monitor all of them simultaneously while running updates. Your manager thinks GUI tools are for "the weak."

### The Screen Solution

```bash
#!/bin/bash
# multi-server-screen.sh

screen -dmS servers

# Create windows for each server
servers=("web01" "web02" "db01" "db02" "cache01")
for i in "${!servers[@]}"; do
    screen -S servers -X screen -t "${servers[$i]}" $i ssh "${servers[$i]}"
done

# Create a monitoring window with splits
screen -S servers -X screen -t "monitoring" 10
screen -S servers -X select 10
screen -S servers -X split
screen -S servers -X focus down
screen -S servers -X screen -t "htop" 11 htop
screen -S servers -X split
screen -S servers -X focus down
screen -S servers -X screen -t "logs" 12 tail -f /var/log/syslog

screen -r servers
```

### The Tmux Solution

```bash
#!/bin/bash
# multi-server-tmux.sh

tmux new-session -d -s servers

# Create windows for each server
servers=("web01" "web02" "db01" "db02" "cache01")
for server in "${servers[@]}"; do
    tmux new-window -t servers -n "$server" "ssh $server"
done

# Create monitoring dashboard
tmux new-window -t servers -n "monitor"
tmux split-window -t servers:monitor -h
tmux split-window -t servers:monitor.1 -v
tmux send-keys -t servers:monitor.0 'htop' Enter
tmux send-keys -t servers:monitor.1 'watch df -h' Enter
tmux send-keys -t servers:monitor.2 'tail -f /var/log/syslog' Enter

# Create synchronized command window for mass updates
tmux new-window -t servers -n "broadcast"
for server in "${servers[@]}"; do
    tmux split-window -t servers:broadcast "ssh $server"
    tmux select-layout -t servers:broadcast tiled
done
tmux setw -t servers:broadcast synchronize-panes on

tmux attach -t servers
```

**Verdict:** Tmux wins with synchronized panes for mass command execution.

## Scenario 2: The Development Environment

You're working on a full-stack application with frontend, backend, database, and various services. You need everything running and visible.

### The Screen Approach

```bash
# ~/.screenrc.development
source $HOME/.screenrc

# Define layouts
layout new dev
screen -t "editor" 0 vim
screen -t "frontend" 1
screen -t "backend" 2
screen -t "database" 3
screen -t "git" 4
screen -t "tests" 5

# Startup commands
at 1 stuff "cd frontend && npm run dev\n"
at 2 stuff "cd backend && npm run server\n"
at 3 stuff "docker-compose up postgres redis\n"
at 4 stuff "git status\n"

# Window monitoring
at 1 monitor on
at 2 monitor on
at 3 monitor on

# Start with editor
select 0
```

### The Tmux Approach

```yaml
# ~/.tmuxinator/dev.yml
name: dev
root: ~/projects/myapp

windows:
  - editor:
      layout: main-horizontal
      panes:
        - vim
        - guard # file watcher

  - services:
      layout: even-horizontal
      panes:
        - frontend:
          - cd frontend
          - npm run dev
        - backend:
          - cd backend
          - npm run server
        - docker:
          - docker-compose up

  - database:
      layout: main-vertical
      panes:
        - psql myapp_dev
        - redis-cli

  - git:
      layout: main-horizontal
      panes:
        - git status
        - tig # git history viewer

  - tests:
      layout: even-horizontal
      panes:
        - npm run test:watch
        - npm run e2e:watch
```

```bash
# Launch with tmuxinator
$ tmuxinator start dev
```

**Verdict:** Tmux with tmuxinator provides more structure and repeatability.

## Scenario 3: The Production Incident

It's 3 AM. Production is down. You need to investigate logs, run queries, restart services, and document everything while the incident commander breathes down your neck.

### Emergency Response with Screen

```bash
#!/bin/bash
# incident-response-screen.sh

INCIDENT_ID=$(date +%Y%m%d_%H%M%S)
LOG_DIR="$HOME/incidents/$INCIDENT_ID"
mkdir -p "$LOG_DIR"

# Start screen with logging
screen -L -Logfile "$LOG_DIR/session.log" -S "incident_$INCIDENT_ID"

# Create investigation windows
screen -S "incident_$INCIDENT_ID" -X screen -t "prod-web" 0 ssh prod-web01
screen -S "incident_$INCIDENT_ID" -X screen -t "prod-db" 1 ssh prod-db01
screen -S "incident_$INCIDENT_ID" -X screen -t "logs" 2 ssh prod-log01
screen -S "incident_$INCIDENT_ID" -X screen -t "monitoring" 3
screen -S "incident_$INCIDENT_ID" -X screen -t "notes" 4 vim "$LOG_DIR/notes.md"

# Set up monitoring window
screen -S "incident_$INCIDENT_ID" -X select 3
screen -S "incident_$INCIDENT_ID" -X stuff "watch -n 1 'curl -s https://api.status.io/status'\n"

# Attach to session
screen -r "incident_$INCIDENT_ID"

echo "Session logging to: $LOG_DIR/session.log"
```

### Emergency Response with Tmux

```bash
#!/bin/bash
# incident-response-tmux.sh

INCIDENT_ID=$(date +%Y%m%d_%H%M%S)
LOG_DIR="$HOME/incidents/$INCIDENT_ID"
mkdir -p "$LOG_DIR"

# Start tmux session
tmux new-session -d -s "incident_$INCIDENT_ID"

# Enable logging
tmux pipe-pane -t "incident_$INCIDENT_ID" -o "cat >> $LOG_DIR/session.log"

# Create incident response layout
tmux rename-window -t "incident_$INCIDENT_ID:0" "command"
tmux split-window -t "incident_$INCIDENT_ID:0" -h -p 30

# Logs window
tmux new-window -t "incident_$INCIDENT_ID" -n "logs"
tmux split-window -t "incident_$INCIDENT_ID:logs" -v
tmux send-keys -t "incident_$INCIDENT_ID:logs.0" "ssh prod-log01 'tail -f /var/log/app/error.log'" Enter
tmux send-keys -t "incident_$INCIDENT_ID:logs.1" "ssh prod-log02 'tail -f /var/log/nginx/access.log'" Enter

# Monitoring window
tmux new-window -t "incident_$INCIDENT_ID" -n "monitoring"
tmux split-window -t "incident_$INCIDENT_ID:monitoring" -h
tmux split-window -t "incident_$INCIDENT_ID:monitoring.0" -v
tmux send-keys -t "incident_$INCIDENT_ID:monitoring.0" "watch -n 1 ./check_services.sh" Enter
tmux send-keys -t "incident_$INCIDENT_ID:monitoring.1" "htop" Enter
tmux send-keys -t "incident_$INCIDENT_ID:monitoring.2" "watch kubectl get pods" Enter

# Notes window
tmux new-window -t "incident_$INCIDENT_ID" -n "notes"
tmux send-keys -t "incident_$INCIDENT_ID:notes" "vim $LOG_DIR/incident_notes.md" Enter

# Status bar with incident info
tmux set -t "incident_$INCIDENT_ID" status-left "#[bg=red,fg=white] INCIDENT: $INCIDENT_ID #[default] "

tmux attach -t "incident_$INCIDENT_ID"
```

**Verdict:** Both work, but Tmux's better layout management helps in chaos.

## Scenario 4: Pair Programming Session

You need to collaborate with a colleague on debugging a complex issue. They're in a different timezone, and screen sharing makes everyone dizzy.

### Screen Collaboration

```bash
# On the server both can access
# Person 1 (owner):
$ screen -S debug-session
$ Ctrl+A :multiuser on
$ Ctrl+A :acladd colleague_username
$ Ctrl+A :aclchg colleague_username +rwx "#?"

# Person 2 (colleague):
$ ssh shared-server
$ screen -x person1/debug-session

# Both can now type and see the same thing
# For read-only observation:
$ Ctrl+A :aclchg colleague_username -w "#"
```

### Tmux Collaboration

```bash
# Using tmate (Tmux fork for sharing)
# Person 1:
$ tmate
$ tmate show-messages
# Copy the ssh session ID

# Person 2:
$ ssh session-id@ny4.tmate.io

# Or traditional tmux on shared server:
# Person 1:
$ tmux new -s debug
$ chmod 777 /tmp/tmux-$(id -u)/default

# Person 2:
$ tmux attach -t debug

# For read-only:
$ tmux attach -t debug -r
```

**Verdict:** Screen's ACL system is more sophisticated, tmate is most convenient.

## Scenario 5: The Long-Running Data Migration

You're migrating terabytes of data. It'll take 48 hours. Your laptop battery lasts 4. Your anxiety lasts forever.

### Screen Reliability Setup

```bash
#!/bin/bash
# migration-screen.sh

# Create a bulletproof session
screen -dmS migration

# Main migration window
screen -S migration -X screen -t "migrate" 0 bash
screen -S migration -X stuff "
set -e
LOG_FILE=/var/log/migration_$(date +%Y%m%d).log
exec 1> >(tee -a \$LOG_FILE)
exec 2>&1
echo 'Migration started at $(date)'
./migrate_data.sh
echo 'Migration completed at $(date)'
"

# Monitoring window
screen -S migration -X screen -t "progress" 1 bash
screen -S migration -X stuff "watch -n 10 'du -sh /data/; df -h; tail -n 20 /var/log/migration_*.log'\n"

# Safety window (in case main dies)
screen -S migration -X screen -t "backup" 2 bash
screen -S migration -X stuff "
while true; do
  if ! pgrep -f migrate_data.sh > /dev/null; then
    echo 'Migration died! Restarting...'
    ./migrate_data.sh --resume
  fi
  sleep 60
done
"

screen -r migration
```

### Tmux Reliability Setup

```bash
#!/bin/bash
# migration-tmux.sh

# Create session with auto-save
tmux new-session -d -s migration

# Enable continuum for auto-restore
cat >> ~/.tmux.conf << EOF
set -g @plugin 'tmux-plugins/tmux-resurrect'
set -g @plugin 'tmux-plugins/tmux-continuum'
set -g @continuum-restore 'on'
set -g @continuum-save-interval '5'
EOF

# Main migration
tmux send-keys -t migration "
LOG_FILE=/var/log/migration_\$(date +%Y%m%d).log
(
  echo 'Starting migration at '\$(date)
  ./migrate_data.sh --verbose
  echo 'Completed at '\$(date)
) 2>&1 | tee \$LOG_FILE
" Enter

# Monitor pane
tmux split-window -t migration -h
tmux send-keys -t migration.1 "
while true; do
  clear
  echo '=== Migration Progress ==='
  du -sh /data/
  echo ''
  echo '=== Disk Space ==='
  df -h /data
  echo ''
  echo '=== Latest Log ==='
  tail -n 10 /var/log/migration_*.log
  sleep 10
done
" Enter

# Alert pane
tmux split-window -t migration.0 -v
tmux send-keys -t migration.2 "
while true; do
  if ! pgrep -f migrate_data.sh > /dev/null; then
    notify-send 'Migration stopped!'
    echo 'Migration process died at '\$(date)
  fi
  sleep 30
done
" Enter

tmux attach -t migration
```

**Verdict:** Both are reliable, Tmux's plugin system adds safety nets.

## Scenario 6: The Teaching Environment

You're teaching a workshop on command-line tools. 20 students need to see what you're doing, and you need to see if they're stuck.

### Screen for Teaching

```bash
# Teacher setup
screen -S workshop
Ctrl+A :multiuser on
Ctrl+A :acladd student1,student2,student3...
Ctrl+A :aclchg student1,student2,student3 -w "#"  # Read-only by default

# Student connection
ssh workshop-server
screen -x teacher/workshop

# Give specific student write access for demonstration
Ctrl+A :aclchg student1 +w "#"
```

### Tmux for Teaching

```bash
# Teacher setup with tmux and custom status
tmux new -s workshop
tmux set -g status-right '#[fg=yellow]Workshop: Linux Basics #[fg=green]| Students: #{session_attached} |'

# Broadcast window for demonstrations
tmux new-window -n demo

# Student practice window
tmux new-window -n practice
tmux send-keys "# Try the commands here" Enter

# Using tmux-broadcast for teaching
git clone https://github.com/teaching-tools/tmux-broadcast
./tmux-broadcast start workshop

# Students connect via web interface
echo "Students: Connect to http://workshop.local:8080"
```

**Verdict:** Screen for traditional terminal access, Tmux for modern web-based teaching.

## Scenario 7: The CI/CD Pipeline Monitor

You need to monitor multiple CI/CD pipelines, deployment status, and be ready to intervene when something breaks.

### Screen CI/CD Dashboard

```bash
# ~/.screenrc.cicd
startup_message off
hardstatus alwayslastline
hardstatus string '%{= kG}[ CI/CD Monitor ]%{= kw}%-Lw%{= BW}%n %t%{-}%+Lw %='

screen -t "Jenkins" 0 watch -n 30 'curl -s http://jenkins/api/json | jq .jobs[].color'
screen -t "GitLab" 1 watch -n 30 'gitlab-cli pipeline list'
screen -t "Deploy" 2 ssh deploy-server
screen -t "Logs" 3 tail -f /var/log/deployment.log
screen -t "Kubectl" 4 watch kubectl get deployments
screen -t "Alerts" 5 tail -f /var/log/alerts.log

# Auto-start monitoring
select 0
```

### Tmux CI/CD Dashboard

```bash
#!/bin/bash
# cicd-dashboard.sh

tmux new-session -d -s cicd

# Pipeline status window
tmux rename-window -t cicd:0 "pipelines"
tmux split-window -t cicd:0 -h -p 50
tmux split-window -t cicd:0.0 -v -p 50
tmux split-window -t cicd:0.2 -v -p 50

# Set up each pane
tmux send-keys -t cicd:0.0 'watch -n 30 "jenkins-cli list-jobs | grep -E \[BUILDING\|FAILED\]"' Enter
tmux send-keys -t cicd:0.1 'watch -n 30 "gitlab pipeline list --mine"' Enter
tmux send-keys -t cicd:0.2 'watch -n 30 "gh run list"' Enter
tmux send-keys -t cicd:0.3 'watch -n 30 "circleci project list"' Enter

# Deployments window
tmux new-window -t cicd -n "deployments"
tmux send-keys -t cicd:deployments 'watch -n 10 kubectl rollout status deployment --all-namespaces' Enter

# Metrics window
tmux new-window -t cicd -n "metrics"
tmux split-window -t cicd:metrics -h
tmux send-keys -t cicd:metrics.0 'prometheus-cli query up' Enter
tmux send-keys -t cicd:metrics.1 'grafana-cli dashboard list' Enter

tmux attach -t cicd
```

**Verdict:** Tmux's superior pane management makes complex dashboards easier.

## Scenario 8: The Security Incident Response

Security breach detected. You need to investigate, contain, and document everything for the post-mortem.

### Screen Security Response

```bash
#!/bin/bash
# security-incident.sh

INCIDENT="SEC_$(date +%Y%m%d_%H%M)"
screen -L -Logfile "~/security/$INCIDENT.log" -S $INCIDENT

# Investigation windows
screen -S $INCIDENT -X screen -t "firewall" 0 ssh firewall
screen -S $INCIDENT -X screen -t "ids" 1 ssh ids-server
screen -S $INCIDENT -X screen -t "affected" 2 ssh compromised-host
screen -S $INCIDENT -X screen -t "logs" 3
screen -S $INCIDENT -X screen -t "tcpdump" 4

# Start packet capture
screen -S $INCIDENT -X at 4 stuff "sudo tcpdump -i eth0 -w $INCIDENT.pcap\n"

# Log analysis
screen -S $INCIDENT -X at 3 stuff "grep -E 'unauthorized|breach|attack' /var/log/* | tee $INCIDENT_analysis.txt\n"

screen -r $INCIDENT
```

### Tmux Security Response

```bash
#!/bin/bash
# security-tmux.sh

INCIDENT="SEC_$(date +%Y%m%d_%H%M)"
tmux new-session -d -s $INCIDENT

# Set up recording
tmux pipe-pane -t $INCIDENT -o "cat >> ~/security/$INCIDENT.log"

# Investigation layout
tmux split-window -t $INCIDENT -h -p 30
tmux split-window -t $INCIDENT.1 -v

# Network monitoring
tmux send-keys -t $INCIDENT.0 "sudo tcpdump -i any -n 'port 443 or port 80'" Enter

# Process monitoring
tmux send-keys -t $INCIDENT.1 "watch -n 1 'ps aux --sort=-%cpu | head -20'" Enter

# Connection monitoring
tmux send-keys -t $INCIDENT.2 "watch -n 1 'netstat -tuln | grep ESTABLISHED'" Enter

# Create forensics window
tmux new-window -t $INCIDENT -n forensics
tmux send-keys -t $INCIDENT:forensics "
mkdir ~/security/$INCIDENT
cd ~/security/$INCIDENT
# Collect evidence
sudo cp -r /var/log ./logs_backup
sudo netstat -an > connections.txt
sudo ps auxww > processes.txt
echo 'Evidence collected'
" Enter

tmux attach -t $INCIDENT
```

**Verdict:** Both work well; choice depends on team familiarity during crisis.

## Best Practices for Different Scenarios

### For Development
- Use Tmux with tmuxinator or similar tools
- Create project-specific configurations
- Integrate with your IDE/editor
- Use plugins for git status, test results

### For System Administration
- Screen for legacy systems and quick tasks
- Tmux for complex monitoring setups
- Always name your sessions meaningfully
- Enable logging for audit trails

### For Emergency Response
- Have pre-written scripts ready
- Enable automatic logging
- Use status lines to show critical info
- Keep sessions simple and focused

### For Collaboration
- Screen for multi-user with permissions
- Tmate for easy remote sharing
- Document your keyboard shortcuts
- Use visual indicators for who's in control

### For Long-Running Tasks
- Always use named sessions
- Enable monitoring/activity alerts
- Set up automatic recovery scripts
- Use screen's or tmux's resurrect features

## Conclusion: The Right Tool for the Job

After all these scenarios, the pattern is clear:
- **Screen** excels at simplicity, reliability, and multi-user scenarios
- **Tmux** shines with complex layouts, modern features, and automation

The best practitioners know both and choose based on:
1. What's available
2. Team familiarity
3. Specific feature needs
4. Personal preference

Remember: A multiplexer in use is worth two in theory. Pick one, master it, and stop worrying about whether you made the "right" choice. You did.

---

*Next: [Chapter 9 - Migration Guide: Switching Sides](09-migration.md)*

*In which we help you betray your current multiplexer with minimal guilt*