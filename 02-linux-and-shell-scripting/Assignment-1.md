# Assignment – Group 1

## EC2 Setup, Secure Access & Linux Command Practice

---

## Objective

By the end of this assignment you will be able to:

* Create a **Security Group** with correct inbound/outbound rules
* Create an **ED25519 (.pem)** SSH key pair and manage permissions safely
* Launch a **t2.micro Ubuntu EC2**
* Connect using SSH (Mac/Linux terminal or MobaXterm on Windows)
* Execute **50+ essential Linux commands** (navigation, files, editing, permissions, networking, processes, logs)

---

## Task 1: Create a Security Group (SG) — Required Ports

### Goal

Design one SG and attach it to your EC2 instance.

### Inbound Rules (MUST)

| Purpose   | Protocol |       Port | Source                 |
| --------- | -------- | ---------: | ---------------------- |
| SSH Admin | TCP      |         22 | **Your Public IP /32** |
| Web       | TCP      |         80 | 0.0.0.0/0              |
| App Range | TCP      | 3000–10000 | 0.0.0.0/0              |

### Outbound Rules

* Allow **All traffic** (default)

### Steps

1. AWS Console → EC2 → **Security Groups**
2. Create SG: `devops-sg-01`
3. Add inbound rules exactly as above
4. Save

### Validation

* SG exists and has 3 inbound rules
* SSH source is **your IP only**, not 0.0.0.0/0

---

## Task 2: Create SSH Key Pair — ED25519 + PEM

### Goal

Create and download a private key to your local machine.

### Instructions

* Key type: **ED25519**
* File format: **PEM**
* Download private key once and store safely

### Local Key Safety (Mandatory)

On your laptop:

```bash
chmod 400 devops-key.pem
```

✅ This prevents “UNPROTECTED PRIVATE KEY FILE” errors.

---

## Task 3: Launch EC2 Instance (Ubuntu + t2.micro)

### Goal

Provision a Ubuntu EC2 and attach SG + key.

### Instructions

* AMI: Ubuntu 20.04 / 22.04
* Type: t2.micro
* Attach:

  * SG: `devops-sg-01`
  * Key: `devops-key.pem`
* Enable auto-assign public IP

### Naming

* Instance Name: `devops-linux-01`

### Validation

* Instance is **Running**
* Public IP is present
* SG attached is correct

---

## Task 4: Connect to EC2 via SSH

### Mac/Linux terminal

```bash
ssh -i devops-key.pem ubuntu@<public-ip>
```

### Windows (MobaXterm)

* Session → SSH
* Remote host: `<public-ip>`
* Username: `ubuntu`
* Advanced SSH settings → Use private key: `devops-key.pem`

### Validation Commands

Run inside EC2:

```bash
whoami
hostname
pwd
```

## Task 5: Linux Commands Practice 

### Rule

Run commands in order, **observe output**, and write 1-line meaning in your notes.

---

### A) System Basics

```bash
# Shows the currently logged-in username
whoami

# Shows your user ID (UID), group ID (GID) and groups you belong to
id

# Shows the machine hostname (server name)
hostname

# Shows kernel version + architecture + OS details (full system identity)
uname -a

# Shows OS distribution info (Ubuntu version, name, etc.)
cat /etc/os-release

# Shows how long system is running + current load averages
uptime

# Shows current date and time
date

# Shows time settings, timezone, NTP sync status (system clock details)
timedatectl
```

---

### B) Navigation & Listing

```bash
# Prints current working directory (where you are)
pwd

# Lists files/folders in current directory
ls

# Lists files with permissions, owner, size, time (detailed listing)
ls -l

# Lists all files including hidden ones (those starting with .)
ls -a

# Detailed listing but file sizes in human readable format (KB/MB/GB)
ls -lh

# Change directory to root of filesystem
cd /

# Change directory to your home directory
cd ~

# Move one directory up
cd ..

# Jump back to the previous directory you were in
cd -

# Shows directory structure in a tree format (2 levels), or prints message if not installed
tree -L 2 2>/dev/null || echo "tree not installed"
```

---

### C) Create Files & Directories

```bash
# Creates a folder named devops-lab in current directory
mkdir devops-lab

# Moves into the devops-lab directory
cd devops-lab

# Creates multiple directories at once: day1, day2, day3
mkdir day1 day2 day3

# Creates nested directories (projects/app/logs). -p creates parents if missing
mkdir -p projects/app/logs

# Creates 3 empty files (or updates timestamp if they exist)
touch file1.txt file2.txt notes.md

# Writes "hello devops" into file1.txt (overwrites if file already has content)
echo "hello devops" > file1.txt

# Appends "line2" to file1.txt (does not overwrite existing content)
echo "line2" >> file1.txt

# Displays the contents of file1.txt on screen
cat file1.txt

# Copies file1.txt into a new file called file1-copy.txt
cp file1.txt file1-copy.txt

# Renames (moves) file2.txt to renamed-file2.txt
mv file2.txt renamed-file2.txt

# Deletes the file renamed-file2.txt
rm renamed-file2.txt

# Removes directory day3 only if empty, else prints message
rmdir day3 2>/dev/null || echo "day3 not empty or already removed"
```

---

### D) View File Content Properly

```bash
# Prints file content (empty now unless you added something)
cat notes.md

# Shows first 5 lines of file1.txt
head -n 5 file1.txt

# Shows last 5 lines of file1.txt
tail -n 5 file1.txt

# Counts number of lines in file1.txt
wc -l file1.txt

# Counts number of words in file1.txt
wc -w file1.txt

# Prints file content with line numbers (great for debugging configs)
nl -ba file1.txt

# Opens file in a scrollable viewer (press q to quit)
less file1.txt
```

---

### E) Edit Files Using VI (Mandatory mini-lab)

#### Step 1: Create a file and open in vi

```bash
# Opens (or creates) profile.txt in the vi editor
vi profile.txt
```

#### Step 2: Do these inside vi

* Press `i` → insert mode
* Write:

  ```
  Name: <YourName>
  Role: DevOps
  ```
* Press `ESC`
* Type `:wq` and Enter (write + quit)

#### Validate

```bash
# Displays the saved file to confirm vi edit worked
cat profile.txt
```

---

### F) Permissions & Ownership

```bash
# Shows current files and permissions in long format
ls -l

# Sets file1.txt permissions to rw-r--r-- (owner read/write, others read)
chmod 644 file1.txt

# Sets profile.txt permissions to rw------- (only owner can read/write)
chmod 600 profile.txt

# Sets projects directory to rwxr-xr-x (owner full, others can read/enter)
chmod 755 projects

# Recursively applies permissions to all files/folders inside projects
chmod -R 755 projects

# Shows detailed metadata of file1.txt (size, inode, permissions, timestamps)
stat file1.txt

# Shows default permission mask (affects new file/dir permissions)
umask
```

### F-2) Ownership Management using `chown` (5 commands)

```bash
# Changes owner of file1.txt to user ubuntu
sudo chown ubuntu file1.txt
```

👉 Use this when a file is created by root or another user and you want ownership back.
---

```bash
# Changes owner and group of profile.txt to ubuntu:ubuntu
sudo chown ubuntu:ubuntu profile.txt
```

👉 Common in application deployments where both user and group must match.
---

```bash
# Changes only the group ownership of file1-copy.txt to ubuntu
sudo chown :ubuntu file1-copy.txt
```

👉 Useful when multiple users share files via a common group.
---

```bash
# Recursively changes owner and group of projects directory and all contents
sudo chown -R ubuntu:ubuntu projects
```

👉 Very common after copying code or extracting archives as root.
---

```bash
# Changes ownership using numeric UID:GID (example: 1000:1000)
sudo chown 1000:1000 notes.md
```

👉 Used in containers, scripts, or when usernames don’t exist.
---

### G) Search & Filters (grep/find)

```bash
# Searches for the word "hello" inside file1.txt and prints matching lines
grep "hello" file1.txt

# Searches for "line" and shows line numbers of matches
grep -n "line" file1.txt

# Case-insensitive search (HELLO matches hello)
grep -i "HELLO" file1.txt

# Shows all lines that do NOT contain "line2"
grep -v "line2" file1.txt

# Finds all files under current directory
find . -type f

# Finds all .txt files under current directory
find . -type f -name "*.txt"

# Finds all directories under current directory
find . -type d

# Finds all files whose size is greater than 0 bytes
find . -type f -size +0c

# Lists some log files in /var/log (errors suppressed), shows only first few results
find /var/log -type f 2>/dev/null | head
```

---

### H) Disk + Memory + Processes

```bash
# Shows RAM usage in MB (free/used/cache)
free -m

# Shows filesystem disk usage in human readable format
df -h

# Shows disk usage of current folder (total size)
du -sh .

# Lists block devices (disks, partitions, mount points)
lsblk

# Shows processes for your current shell session
ps

# Shows all running processes for all users (first few lines only)
ps aux | head

# Shows real-time process view (press q to quit)
top

# Prints PID of sshd process if available, else prints message
pidof sshd || echo "pidof not available"
```

---

### I) Networking

```bash
# Shows network interfaces and IP addresses
ip a

# Shows routing table (how packets leave the machine)
ip route

# Shows listening ports/services with process info (first lines only)
ss -tulnp | head

# Sends HTTP HEAD request and shows response headers (connectivity test)
curl -I http://example.com | head

# Pings a public DNS IP (checks raw internet connectivity)
ping -c 3 8.8.8.8

# Pings google.com (checks both DNS + internet connectivity)
ping -c 3 google.com

# Resolves DNS using nslookup if installed, else prints message
nslookup google.com 2>/dev/null || echo "nslookup not installed"

# Resolves DNS using dig if installed, else prints message
dig google.com 2>/dev/null || echo "dig not installed"
```

---

### J) Logs & Services

```bash
# Shows SSH service status (running/active, logs, PID)
systemctl status ssh

# Shows recent logs for ssh service (no pager, first few lines)
journalctl -u ssh --no-pager | head

# Shows last 20 authentication log lines (SSH logins, sudo attempts)
sudo tail -n 20 /var/log/auth.log

# Shows last 20 system log lines (general OS messages)
sudo tail -n 20 /var/log/syslog

# Shows recent kernel messages (hardware, drivers, boot/runtime events)
dmesg | tail -n 20
```

