# Linux Notes

## What is an operating system

An OS is the layer between hardware and applications. It's made up of the **kernel** (talks directly to hardware, manages CPU/memory/I/O) plus userland tools — a shell/CLI at minimum, optionally a GUI on top.

## What is Linux

A free, open-source Unix-like operating system kernel, originally created by Linus Torvalds in 1991. "Linux" as commonly used refers to a full OS distribution built around that kernel (Ubuntu, Amazon Linux, CentOS, etc.).

## Filesystem basics

| Path         | Purpose                                              |
| ------------ | ---------------------------------------------------- |
| `/bin`       | Basic programs (`ls`, `cd`, `pwd`)                   |
| `/sbin`      | System programs (`fdisk`, `systemctl`, `journalctl`) |
| `/etc`       | Configuration files                                  |
| `/tmp`       | Temporary files                                      |
| `/usr/bin`   | Installed applications (`apt`, `nmap`, etc.)         |
| `/usr/share` | Application support/data files                       |
| `/home`      | Personal user directories (e.g. `/home/pritam`)      |
| `/root`      | Home directory of the superuser (admin)              |

## Core commands

| Command  | Purpose                                     | Example                                                     |
| -------- | ------------------------------------------- | ----------------------------------------------------------- |
| `ls`     | List files/folders                          | `ls -al` (all, with details, including hidden)              |
| `cd`     | Change directory                            | `cd ..` (go up one level)                                   |
| `pwd`    | Print working directory                     | `pwd`                                                       |
| `man`    | Full manual for a command                   | `man pwd`                                                   |
| `mkdir`  | Create a directory                          | `mkdir "new folder"` (quotes needed if the name has spaces) |
| `cp`     | Copy a file/folder                          | `cp filename destination`                                   |
| `mv`     | Move (or rename) a file                     | `mv filename destination`                                   |
| `rm`     | Remove a file/directory                     | `rm -r foldername` (`-r` = recursive, needed for folders)   |
| `cat`    | Print file contents                         | `cat hello.html`                                            |
| `nano`   | Terminal text editor                        | `nano hello.html`                                           |
| `touch`  | Create an empty file                        | `touch hello.py`                                            |
| `whoami` | Show current username                       | `whoami`                                                    |
| `w`      | Show who's logged in and what they're doing | `w`                                                         |

## Permissions

**`chmod`** — changes what actions are allowed (read/write/execute) on a file.

```bash
chmod +x filename    # add execute permission
chmod -x filename    # remove execute permission
```

**`chown`** — changes who owns a file (user and/or group). Every file has exactly one owner and one group; `chown` reassigns that, separate from `chmod` (which controls _what's allowed_, not _who owns it_).

```bash
chown user:group filename
chown -R www-data:www-data /var/www/html/
```

**Why this matters in practice:** if a file is uploaded via `sudo` or as `root`, a lower-privileged process (e.g. the user running a web server) may not have access to it even though the file exists and looks fine. `chown` fixes that mismatch — one of the most common real-world permission fixes.

## Users

```bash
sudo adduser ram              # interactive, beginner-friendly, creates home dir
sudo useradd -m -s /bin/bash ram   # lower-level equivalent, more control
sudo passwd ram                # set password separately (needed with useradd)
sudo usermod -aG sudo ram      # grant sudo access — -a appends to group,
                                # don't forget it or you'll wipe the user's other groups
```

**Logging in as another user:**

```bash
su - ram                    # switch to ram if already on the server (the "-" loads their environment properly)
ssh ram@server-ip -p port   # log in fresh over SSH
```

## Running scripts

```bash
./hello        # execute a shell script directly (needs execute permission)
bash hello      # execute via bash explicitly
```

## Package management

Package managers pull from a remote repository index. That index can go stale, so **update before upgrade** — upgrading against an outdated index can fail or install the wrong version.

```bash
sudo apt update      # refresh the package index (metadata only)
sudo apt upgrade     # actually upgrade installed packages
sudo apt install gedit   # install a specific package (name must be exact)
```

## Processes

A process is a running instance of a program — the OS assigns it a PID, and tracks memory, CPU time, and owner.

```bash
ps aux | grep node    # find a specific running process
top                    # live view of processes and resource usage
kill 2777              # ask a process to terminate gracefully (SIGTERM)
kill -9 2777           # force-kill immediately (SIGKILL)
```

| Signal  | Command        | Meaning                              |
| ------- | -------------- | ------------------------------------ |
| SIGHUP  | `kill -1 PID`  | Hang up / reload                     |
| SIGINT  | `kill -2 PID`  | Interrupt (same as Ctrl+C)           |
| SIGKILL | `kill -9 PID`  | Force kill, no cleanup               |
| SIGTERM | `kill -15 PID` | Polite termination request (default) |

## Services (systemd)

Modern Linux manages long-running background services through **systemd**. Each service has a unit file defining how it starts, what user it runs as, and whether it restarts automatically. `systemctl` is how you control it.

```bash
sudo systemctl start nginx      # start now
sudo systemctl enable nginx     # auto-start on every reboot
sudo systemctl status nginx     # check if it's running + recent logs
sudo systemctl restart nginx    # stop then start (e.g. after a config change)
```

## Logs and troubleshooting

Logs are timestamped records of what happened — errors, warnings, requests. System services log via `journald` (queried with `journalctl`); many applications also write directly to `/var/log`.

```bash
journalctl -u nginx -n 50           # last 50 lines for a service
journalctl -u nginx -f              # live-tail new entries
tail -f /var/log/syslog             # live-tail a log file
grep -i "error" /var/log/app.log    # search for a specific pattern
```

**A real troubleshooting sequence, not just commands in isolation:**

1. `systemctl status <service>` — is it even running?
2. `journalctl -u <service> --since "10 min ago"` — what happened right before it died?
3. `df -h` — is the disk full? (a very common silent cause of crashes)
4. `free -h` — is memory exhausted? (common on small instance types like t2.micro)
5. Check the application's own logs — systemd only knows a process died, not _why_ in application terms.

## Finding files (`find`)

```bash
find . -type d                       # directories only
find . -type f                       # regular files only
find . -type f -size 100c            # exact size, in bytes (c = bytes)
find . -type f -size +1M             # larger than 1MB
find . -type f -user ec2-user        # owned by a specific user
find . -type f -group apache         # owned by a specific group
find . -type f -perm 644             # by permission mode
find . -name "index.html"            # by name

# Combined example: root-owned regular files over 1MB
find . -type f -user root -size +1M
```

`find .` searches from the current directory down; `find /` searches the entire filesystem from root.
