# Linux Process Management Commands – Complete Quick Reference

A practical reference for process monitoring, management, performance analysis, and troubleshooting in Linux.

---

# 1. View Running Processes

## ps

Display running processes.

```bash
ps
ps -ef
ps aux
ps aux | grep nginx
```

Common fields:

- PID – Process ID
- PPID – Parent Process ID
- USER – Process owner
- %CPU – CPU usage
- %MEM – Memory usage
- CMD – Executed command

---

## pgrep

Find process IDs by name.

```bash
pgrep sshd
pgrep -l java
```

---

## pidof

Get the PID of a process.

```bash
pidof nginx
pidof sshd
```

---

# 2. Real-Time Process Monitoring

## top

Interactive process monitor.

```bash
top
```

Useful keys:

```text
P = Sort by CPU
M = Sort by Memory
k = Kill process
u = Filter by user
```

---

## htop

Enhanced version of top.

```bash
htop
```

Features:

- Colorized interface
- Tree view
- Easy process management
- CPU and memory graphs

---

## atop

Advanced process and system monitor.

```bash
atop
```

Monitors:

- CPU
- Memory
- Disk
- Network
- Process history

---

## glances

All-in-one monitoring utility.

```bash
glances
```

---

# 3. Process Tree

## pstree

Display parent-child process relationships.

```bash
pstree
pstree -p
```

Example:

```text
systemd(1)
├─sshd(1043)
│ └─bash(3210)
└─docker(500)
```

---

# 4. Background and Foreground Jobs

## jobs

Display shell jobs.

```bash
jobs
```

---

## bg

Move a suspended job to the background.

```bash
bg %1
```

---

## fg

Bring a background job to the foreground.

```bash
fg %1
```

---

## nohup

Run a process after logout.

```bash
nohup script.sh &
```

---

# 5. Start Processes

## Run in Background

```bash
python app.py &
```

---

## setsid

Detach a process from the terminal.

```bash
setsid script.sh
```

---

# 6. Kill Processes

## kill

Send signals to a process.

```bash
kill PID
kill -15 PID
kill -9 PID
```

Important signals:

| Signal | Meaning |
|----------|----------|
| 1 | HUP |
| 2 | INT |
| 9 | KILL |
| 15 | TERM |
| 18 | CONT |
| 19 | STOP |

---

## pkill

Kill processes by name.

```bash
pkill nginx
pkill java
```

---

## killall

Kill all matching processes.

```bash
killall firefox
```

---

# 7. Process Priority Management

## nice

Start a process with a custom priority.

```bash
nice -n 10 script.sh
```

Priority range:

```text
-20 = Highest Priority
 19 = Lowest Priority
```

---

## renice

Change priority of a running process.

```bash
renice 5 -p 1234
```

---

# 8. CPU Usage Analysis

## top

```bash
top
```

---

## mpstat

CPU statistics.

```bash
mpstat
mpstat -P ALL
```

---

## sar

Historical CPU performance.

```bash
sar -u
sar -u 1 5
```

---

## pidstat

Per-process CPU statistics.

```bash
pidstat
pidstat -p PID
```

---

# 9. Memory Troubleshooting

## free

Display memory usage.

```bash
free -h
```

---

## vmstat

Memory and CPU statistics.

```bash
vmstat
vmstat 1
```

---

## pmap

View a process memory map.

```bash
pmap PID
pmap -x PID
```

---

## smem

Detailed memory reporting.

```bash
smem
```

---

# 10. Open Files and Resource Usage

## lsof

List open files.

```bash
lsof
lsof -p PID
lsof file.txt
```

Find deleted files still consuming disk space:

```bash
lsof | grep deleted
```

---

## fuser

Identify processes using a file or port.

```bash
fuser /tmp/file
fuser -n tcp 8080
```

---

# 11. Process-to-Port Mapping

## netstat

```bash
netstat -tulpn
```

---

## ss

Modern replacement for netstat.

```bash
ss -tulpn
ss -plant
```

Useful for finding which process owns a port.

---

# 12. Scheduling and Processor Affinity

## taskset

View or set CPU affinity.

```bash
taskset -p PID
taskset -cp 0 PID
```

---

## chrt

View or set real-time scheduling.

```bash
chrt -p PID
```

---

# 13. Systemd Service Management

## systemctl

Start a service:

```bash
systemctl start nginx
```

Stop a service:

```bash
systemctl stop nginx
```

Restart a service:

```bash
systemctl restart nginx
```

Check status:

```bash
systemctl status nginx
```

Show failed services:

```bash
systemctl --failed
```

---

# 14. Logs and Troubleshooting

## journalctl

View systemd logs.

```bash
journalctl -xe
journalctl -u nginx
journalctl -f
```

---

## dmesg

View kernel messages.

```bash
dmesg
dmesg -T
```

Useful for:

- OOM events
- Hardware issues
- Driver problems

---

# 15. Process Debugging

## strace

Trace system calls.

```bash
strace -p PID
```

Launch with tracing:

```bash
strace command
```

Useful for:

- Hanging processes
- Permission issues
- Missing files

---

## ltrace

Trace library calls.

```bash
ltrace command
```

---

## gdb

Debug a running process.

```bash
gdb -p PID
```

---

# 16. Resource Limits

## ulimit

View shell resource limits.

```bash
ulimit -a
```

Examples:

```bash
ulimit -n
ulimit -u
```

Controls:

- Maximum open files
- Maximum user processes
- Stack size

---

# 17. Historical Performance Analysis

## sar

CPU history:

```bash
sar -u
```

Memory history:

```bash
sar -r
```

Context switching:

```bash
sar -w
```

---

# 18. Process States

View process states:

```bash
ps aux
```

Common states:

| State | Meaning |
|---------|---------|
| R | Running |
| S | Sleeping |
| D | Uninterruptible Sleep |
| T | Stopped |
| Z | Zombie |
| X | Dead |

---

# 19. Zombie Process Investigation

Find zombie processes:

```bash
ps -el | grep Z
```

or

```bash
ps aux | grep defunct
```

Find the parent process:

```bash
ps -o ppid= -p <PID>
```

---

# 20. OOM (Out of Memory) Troubleshooting

Check for OOM kills:

```bash
dmesg -T | grep -i oom
```

```bash
journalctl -k | grep -i kill
```

Find top memory-consuming processes:

```bash
ps aux --sort=-%mem | head
```

---

# Common Troubleshooting Workflows

## High CPU Usage

```bash
top
ps -fp <PID>
pstree -p <PID>
strace -p <PID>
```

---

## High Memory Usage

```bash
top
free -h
ps aux --sort=-%mem
pmap -x <PID>
```

---

## Port Already in Use

```bash
ss -tulpn
lsof -i :8080
fuser 8080/tcp
```

---

## Service Not Starting

```bash
systemctl status service
journalctl -u service
journalctl -xe
```

---

## Hung Process

```bash
strace -p PID
lsof -p PID
```

---

## Slow System

```bash
top
htop
vmstat 1
iostat
sar
```

---

# Must-Know Commands for Interviews

```bash
ps
top
htop
pgrep
pidof
pkill
kill
killall
jobs
bg
fg
nohup
pstree
nice
renice
free
vmstat
pidstat
lsof
fuser
ss
systemctl
journalctl
strace
ulimit
sar
```

---

# Interview Tip

For most Linux Administrator, DevOps, SRE, and Platform Engineer interviews, mastery of the following commands covers approximately 90% of real-world process management and troubleshooting scenarios:

```text
ps, top, htop, pgrep, kill, pkill, pstree,
nice, renice, free, vmstat, pidstat,
lsof, ss, systemctl, journalctl, strace,
ulimit, sar
```
