# Assignment – Group 2

## User Management, Group Management & Ownership (Linux Identity & Access)

---

## Objective

In this group, you will learn how Linux manages **users, groups, ownership, and access control**.

By the end of this assignment, you should be able to:

* Create and manage **users**
* Create and manage **groups**
* Control **file and directory ownership**
* Understand **how Linux decides who can access what**
* Debug common **permission denied** issues

This group is **mandatory for production systems** and frequently tested in interviews.

---

## Prerequisites

* Completed **Group-1**
* Logged in to EC2 as `ubuntu` user
* `sudo` access available

Verify:

```bash
whoami
sudo -v
```

---

## Task 1: User Creation & Management

### Goal

Create multiple users and understand how Linux stores and manages them.

---

### Step 1: Create Users

```bash
# Create user devops1 with home directory and bash shell
sudo useradd -m -s /bin/bash devops1
```

```bash
# Create user devops2 with home directory
sudo useradd -m devops2
```

```bash
# Create user tester with comment and home directory
sudo useradd -m -c "QA Tester" tester
```

👉 `-m` → creates home directory
👉 `-s` → defines login shell
👉 `-c` → adds user description

---

### Step 2: Set Passwords

```bash
# Set password for devops1
sudo passwd devops1
```

```bash
# Set password for devops2
sudo passwd devops2
```

```bash
# Set password for tester
sudo passwd tester
```

---

### Step 3: Verify Users

```bash
# Show user entry from /etc/passwd
cat /etc/passwd | grep devops
```

```bash
# Show detailed user info
id devops1
```

```bash
# List home directories
ls -l /home
```

---

### Step 4: Login as Another User

```bash
# Switch to devops1 user
su - devops1
```

```bash
# Verify current user
whoami
```

```bash
# Exit back to ubuntu
exit
```

---

### Validation

* Users exist in `/etc/passwd`
* Home directories exist
* Login works using `su - username`

---

## Task 2: Group Creation & Management

### Goal

Create groups and manage group memberships properly.

---

### Step 1: Create Groups

```bash
# Create a devops group
sudo groupadd devops
```

```bash
# Create a qa group
sudo groupadd qa
```

---

### Step 2: Add Users to Groups

```bash
# Add devops1 to devops group
sudo usermod -aG devops devops1
```

```bash
# Add devops2 to devops group
sudo usermod -aG devops devops2
```

```bash
# Add tester to qa group
sudo usermod -aG qa tester
```

👉 `-aG` → append to group (VERY IMPORTANT)

---

### Step 3: Verify Group Membership

```bash
# Check groups for devops1
groups devops1
```

```bash
# Show group file entry
cat /etc/group | grep devops
```

---

### Validation

* Users appear in correct groups
* Group membership persists after logout/login

---

## Task 3: Ownership & Group-Based Access

### Goal

Understand how ownership and groups control access.

---

### Step 1: Create a Shared Directory

```bash
# Create shared directory
sudo mkdir /opt/shared-data
```

```bash
# Change group ownership to devops
sudo chown :devops /opt/shared-data
```

```bash
# Allow group members full access
sudo chmod 770 /opt/shared-data
```

---

### Step 2: Test Access as Group Member

```bash
# Switch to devops1
su - devops1
```

```bash
# Create file inside shared directory
touch /opt/shared-data/devops1.txt
```

```bash
# Exit back to ubuntu
exit
```

---

### Step 3: Test Access as Non-Member

```bash
# Switch to tester user
su - tester
```

```bash
# Try creating file (should FAIL)
touch /opt/shared-data/tester.txt
```

👉 You should see **Permission denied**

```bash
# Exit back
exit
```

---

### Validation

* devops users can write to directory
* non-devops users are denied

---

## Task 4: File Ownership & chown Practice

### Goal

Practice changing ownership in real scenarios.

---

```bash
# Change ownership of shared directory recursively
sudo chown -R devops1:devops /opt/shared-data
```

```bash
# Verify ownership
ls -ld /opt/shared-data
```

```bash
# Change ownership back to root
sudo chown root:devops /opt/shared-data
```

```bash
# Change only group ownership
sudo chown :qa /opt/shared-data
```

```bash
# Use numeric UID:GID
sudo chown 1000:1000 /opt/shared-data/devops1.txt
```
---

## Task 4.5: `chmod` Deep Practice (Numeric + Symbolic + Real Scenarios)

### Goal

Gain complete control over file and directory permissions using `chmod` and understand how permission changes affect access in real situations.

This task focuses **only on permissions**, not ownership.

---

### Step 1: Prepare Test Files and Directories

```bash
# Create a test directory for chmod practice
mkdir chmod-lab
cd chmod-lab

# Create test files and directories
touch file1.txt file2.txt
mkdir dir1 dir2
```

---

### Step 2: Understand Default Permissions

```bash
# List permissions of newly created files and directories
ls -l
ls -ld dir1
```

👉 Observe:

* Files usually start with `rw-r--r--`
* Directories usually start with `rwxr-xr-x`

---

### Step 3: `chmod` Using Numeric Mode

```bash
# Set file to rw-r--r--
chmod 644 file1.txt
ls -l file1.txt
```

```bash
# Set file to rwx------
chmod 700 file1.txt
ls -l file1.txt
```

```bash
# Set directory to rwxr-x---
chmod 750 dir1
ls -ld dir1
```

👉 Numeric reminder:

* r = 4
* w = 2
* x = 1

---

### Step 4: `chmod` Using Symbolic Mode

```bash
# Add execute permission to owner only
chmod u+x file2.txt
ls -l file2.txt
```

```bash
# Remove write permission from group and others
chmod go-w file2.txt
ls -l file2.txt
```

```bash
# Give read permission to everyone
chmod a+r file2.txt
ls -l file2.txt
```

---

### Step 5: Directory Permission Behavior (Critical)

```bash
# Remove execute permission from directory
chmod 600 dir2
ls -ld dir2
```

```bash
# Try entering directory (should fail)
cd dir2
```

👉 Expected: **Permission denied**

```bash
# Restore execute permission
chmod 700 dir2
cd dir2
pwd
cd ..
```

👉 Key learning:

* Directory needs **x** to enter
* Directory needs **r** to list
* Directory needs **w** to create/delete files

---

### Step 6: Recursive Permission Changes

```bash
# Apply permissions recursively to directory and contents
chmod -R 755 chmod-lab
```

```bash
# Verify recursive change
ls -l
ls -ld dir1
```

---

### Step 7: Permission Denied Simulation & Fix

```bash
# Remove all permissions for group and others
chmod 700 file1.txt
ls -l file1.txt
```

```bash
# Switch to another user
su - devops1
cat ~/chmod-lab/file1.txt
exit
```

👉 Expected: **Permission denied**

```bash
# Fix by allowing group read access
chmod 740 file1.txt
ls -l file1.txt
```

---

### Step 8: Compare `chmod` vs `chown` (Important Concept)

```bash
# Change permissions only
chmod 777 file2.txt
ls -l file2.txt
```

👉 Permissions changed, **owner did not**

```bash
# Change ownership only
sudo chown devops1 file2.txt
ls -l file2.txt
```

👉 Owner changed, **permissions did not**

---

### Validation

* Numeric and symbolic `chmod` both used
* Permission denied error reproduced and fixed
* Directory permission behavior understood
* Recursive permission change verified
* Difference between `chmod` and `chown` clearly observed

---

 **Important Rule for Students**

> Never use `chmod 777` in production.
> This task uses it **only to demonstrate behavior**.

---

## Task 5: User Expiry & Account Control

### Goal

Control user lifecycle (important for security).

---

```bash
# Lock devops2 account
sudo passwd -l devops2
```

```bash
# Unlock devops2 account
sudo passwd -u devops2
```

```bash
# Set password aging policy
sudo chage -m 7 -M 90 -W 7 devops1
```

```bash
# View password aging info
sudo chage -l devops1
```

```bash
# Expire user account on specific date
sudo chage -E 2026-12-31 tester
```

## Task 6: Default User Creation Behavior (Skeleton Files)

### Goal

Understand what files/directories are created automatically for new users.

---

### Steps

```bash
# View default skeleton files copied to new users
ls -la /etc/skel
```

```bash
# Create a new user to observe skeleton behavior
sudo useradd -m -s /bin/bash intern1
sudo passwd intern1
```

```bash
# List intern1 home directory
ls -la /home/intern1
```

### What to Observe

* `.bashrc`, `.profile`, `.bash_logout`
* These files come from `/etc/skel`

---

## Task 7: Primary Group vs Secondary Group

### Goal

Understand the difference between **primary** and **secondary** groups.

---

### Steps

```bash
# Check primary and secondary groups
id devops1
```

```bash
# Check group ownership of files created by devops1
su - devops1
touch primary-test.txt
ls -l primary-test.txt
exit
```

### Key Learning

* File group = user’s **primary group**
* Secondary groups don’t apply by default

---

## Task 8: Change Primary Group of a User

### Goal

Modify a user’s **primary group** and see its effect.

---

### Steps

```bash
# Change primary group of devops1 to devops
sudo usermod -g devops devops1
```

```bash
# Verify change
id devops1
```

```bash
# Login and create a new file
su - devops1
touch primary-group-changed.txt
ls -l primary-group-changed.txt
exit
```

### Observe

* Group ownership now reflects new primary group

---

## Task 9: Removing User from a Group (Safely)

### Goal

Remove group membership without breaking other groups.

---

### Steps

```bash
# Check current groups
groups devops2
```

```bash
# Remove devops2 from devops group
sudo gpasswd -d devops2 devops
```

```bash
# Verify removal
groups devops2
```

### Interview Note

`usermod -G` **overwrites groups** → dangerous
`gpasswd -d` → safe removal

---

## Task 10: Shared Directory with Group Collaboration

### Goal

Simulate a **real DevOps shared workspace**.

---

### Steps

```bash
# Create shared workspace
sudo mkdir /srv/devops-workspace
```

```bash
# Assign devops group
sudo chown :devops /srv/devops-workspace
```

```bash
# Enable group collaboration
sudo chmod 2770 /srv/devops-workspace
```

```bash
# Verify SGID bit
ls -ld /srv/devops-workspace
```

### Explanation

* `2` → SGID
* New files inherit **group ownership automatically**

---

## Task 11: Validate SGID Behavior

### Steps

```bash
# Login as devops1
su - devops1
touch /srv/devops-workspace/devops1-file.txt
ls -l /srv/devops-workspace/devops1-file.txt
exit
```

```bash
# Login as devops2
su - devops2
touch /srv/devops-workspace/devops2-file.txt
ls -l /srv/devops-workspace/devops2-file.txt
exit
```

### Expected

* Both files belong to `devops` group

---

## Task 12: Account Locking & Access Test

### Goal

Verify what happens when an account is locked.

---

### Steps

```bash
# Lock tester account
sudo passwd -l tester
```

```bash
# Attempt to switch user
su - tester
```

👉 Login should fail

```bash
# Unlock account
sudo passwd -u tester
```

---

## Task 13: Delete Users Safely

### Goal

Understand difference between deleting user **with** and **without** home directory.

---

### Steps

```bash
# Delete user but keep home directory
sudo userdel intern1
```

```bash
# Verify home directory still exists
ls /home
```

```bash
# Create user again
sudo useradd -m intern2
sudo passwd intern2
```

```bash
# Delete user and home directory
sudo userdel -r intern2
```

---

## Task 14: Real-World Permission Debugging

### Scenario

> “devops1 cannot write to a directory, but should be able to.”

---

### Steps

```bash
# Create restricted directory
sudo mkdir /data/restricted
sudo chown root:root /data/restricted
sudo chmod 755 /data/restricted
```

```bash
# Test as devops1
su - devops1
touch /data/restricted/test.txt
exit
```

❌ Permission denied

---

### Fix

```bash
sudo chown :devops /data/restricted
sudo chmod 775 /data/restricted
```

```bash
# Retry
su - devops1
touch /data/restricted/test.txt
ls -l /data/restricted
exit
```

---

## Task 15: Audit & Verification Commands

### Goal

Verify that users, groups, and login activity actually exist in the system using Linux audit commands.

---

### Steps

```bash
# Verify that user devops1 exists in the system user database
getent passwd devops1
```

```bash
# Verify that group devops exists and list its members
getent group devops
```

```bash
# Display recent user login activity (audit check)
last | head
```

---

### What to Check

* `getent passwd devops1` must return user details
* `getent group devops` must show group and users
* `last` must show recent login entries

---

### Validation

* User record is present
* Group record is present
* Login activity is visible



