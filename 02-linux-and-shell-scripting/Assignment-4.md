# Assignment – Group 4

## Text Processing & File Search (grep, find, sed, awk)

---

## Objective

In this group, you will learn how Linux engineers **search, filter, transform, and analyze text and files**.

By the end of this group, you should be able to:

* Find files efficiently using `find`
* Search inside files using `grep`
* Modify text using `sed`
* Extract and analyze structured data using `awk`
* Combine commands to solve **real production problems**

This group is **critical for log analysis, debugging, automation, and interviews**.

---

## Prerequisites

* Ubuntu Linux VM
* Basic file navigation knowledge
* `sudo` access (only if needed)

Verify:

```bash
whoami
pwd
```

---

## Setup (Do Once)

### Goal

Create sample files so everyone works on the same data.

```bash
# Create working directory
mkdir -p ~/text-lab
cd ~/text-lab
```

```bash
# Create a sample log file
cat <<EOF > app.log
INFO  user=alice  action=login  status=success
INFO  user=bob    action=login  status=failed
WARN  user=alice  action=upload status=slow
ERROR user=carol  action=delete status=denied
INFO  user=bob    action=logout status=success
ERROR user=alice  action=update status=failed
EOF
```

```bash
# Create a CSV-style file
cat <<EOF > users.csv
id,name,role,salary
1,Alice,DevOps,90000
2,Bob,QA,60000
3,Carol,DevOps,95000
4,Dan,Intern,30000
EOF
```

---

# 🔹 PART 1: `find` – File & Directory Search

---

## Task 1: Find Files by Name

### Goal

Locate files without knowing their exact path.

```bash
# Find file named app.log in current directory tree
find . -name app.log
```

👉 Searches recursively from current directory

---

```bash
# Case-insensitive search
find . -iname "APP.LOG"
```

---

## Task 2: Find by Type (File / Directory)

```bash
# Find all files
find . -type f
```

```bash
# Find all directories
find . -type d
```

👉 `-type f` = files
👉 `-type d` = directories

---

## Task 3: Find by Size & Time

```bash
# Find files larger than 1 KB
find . -type f -size +1k
```

```bash
# Find files modified in last 24 hours
find . -type f -mtime -1
```

---

## Task 4: Find and Execute Command

```bash
# Show size of each file found
find . -type f -exec ls -lh {} \;
```

👉 `{}` → placeholder for found file
👉 `\;` → end of exec command

---

# 🔹 PART 2: `grep` – Search Inside Files

---

## Task 5: Basic Text Search

```bash
# Find lines containing ERROR
grep "ERROR" app.log
```

👉 Searches line by line

---

```bash
# Case-insensitive search
grep -i "error" app.log
```

---

## Task 6: Show Line Numbers & Count

```bash
# Show line numbers
grep -n "INFO" app.log
```

```bash
# Count matching lines
grep -c "ERROR" app.log
```

---

## Task 7: Invert Match (Exclude Lines)

```bash
# Show lines NOT containing INFO
grep -v "INFO" app.log
```

---

## Task 8: Recursive grep

```bash
# Search recursively for "user=alice"
grep -R "user=alice" .
```

---

# 🔹 PART 3: `sed` – Stream Editor (Modify Text)

---

## Task 9: Substitute Text (Preview Only)

```bash
# Replace failed with FAILED (output only)
sed 's/failed/FAILED/' app.log
```

👉 Does NOT change file
👉 Only prints modified output

---

## Task 10: Global Replacement

```bash
# Replace all occurrences in each line
sed 's/INFO/LOG/g' app.log
```

---

## Task 11: In-Place Edit (Dangerous – Understand First)

```bash
# Replace INFO with INFO_LEVEL inside file
sed -i 's/INFO/INFO_LEVEL/g' app.log
```

👉 `-i` modifies file permanently

---

## Task 12: Delete Lines

```bash
# Delete lines containing WARN
sed '/WARN/d' app.log
```

---

## Task 13: Print Specific Lines

```bash
# Print line numbers 2 to 4
sed -n '2,4p' app.log
```

---

# 🔹 PART 4: `awk` – Structured Text Processing

---

## What awk Thinks (Very Important)

* Each line = record
* Space (or delimiter) = field
* `$1`, `$2`, `$3` = columns

---

## Task 14: Print Specific Columns

```bash
# Print first and third column
awk '{print $1, $3}' app.log
```

---

## Task 15: Use Custom Delimiter (CSV)

```bash
# Print name and salary
awk -F',' '{print $2, $4}' users.csv
```

👉 `-F','` → comma delimiter

---

## Task 16: Conditional Filtering

```bash
# Show users with salary > 80000
awk -F',' '$4 > 80000 {print $2, $4}' users.csv
```

---

## Task 17: Skip Header Row

```bash
# Skip first line (header)
awk -F',' 'NR>1 {print $2, $3}' users.csv
```

👉 `NR` = record number

---

## Task 18: Aggregation (Interview Favorite)

```bash
# Sum salaries
awk -F',' 'NR>1 {sum += $4} END {print sum}' users.csv
```

---

# 🔹 PART 5: Combining Commands (Real-World Practice)

---

## Task 19: Find + Grep

```bash
# Find all log files and search for ERROR
find . -name "*.log" -exec grep "ERROR" {} \;
```

---

## Task 20: Grep + Awk

```bash
# Extract usernames from ERROR lines
grep "ERROR" app.log | awk '{print $2}'
```

---

## Task 21: Sed + Grep

```bash
# Mask usernames (preview)
sed 's/user=[a-z]*/user=REDACTED/' app.log | grep ERROR
```

---

## Task 22: Real Log Analysis Mini-Lab

### Goal

Answer: *Which users had failed actions?*

```bash
grep "failed" app.log | awk '{print $2}' | sort | uniq
```

# Assignment – Group 4 (Advanced)

## Text Processing & Log Analysis (Interview Track)

> ⚠️ **Optional / Interview-Focused**
> Attempt this **only after completing Group-4 Core**.

---

## Objective

This advanced track tests whether you can:

* Design command pipelines (not just run them)
* Analyze logs like a production engineer
* Choose the **right tool** (`grep`, `sed`, `awk`, `find`)
* Explain your reasoning (interview expectation)

---

## Rules (Very Important)

1. **Do not jump directly to one-liners**
2. First run commands step-by-step
3. Capture intermediate output
4. Be able to explain *why* you used each tool

---

## Setup (Reuse Core Data)

Use the same files:

* `app.log`
* `users.csv`

---

# 🔴 PART A: Log Analysis Scenarios

---

## Task A1: Error Ownership Analysis

### Problem

> Which users caused errors, and how many times each?

### Expected Thinking Path

1. Filter error lines
2. Extract usernames
3. Count occurrences

### Guided Steps

```bash
# Step 1: Filter ERROR lines
grep "ERROR" app.log
```

```bash
# Step 2: Extract user field
grep "ERROR" app.log | awk '{print $2}'
```

```bash
# Step 3: Count occurrences
grep "ERROR" app.log | awk '{print $2}' | sort | uniq -c
```

---

## Task A2: Failure Trend Detection

### Problem

> List actions that failed and who triggered them.

```bash
# Step 1: Filter failed actions
grep "failed" app.log
```

```bash
# Step 2: Extract user and action
grep "failed" app.log | awk '{print $2, $3}'
```

---

## Task A3: Security Redaction

### Problem

> Mask usernames before sharing logs externally.

```bash
# Replace usernames with REDACTED (preview only)
sed 's/user=[a-z]*/user=REDACTED/g' app.log
```

👉 Explain why `-i` is avoided here.

---

# 🔴 PART B: File System & Security Audits

---

## Task B1: Insecure Permission Audit

### Problem

> Find files with world-writable permissions.

```bash
# Identify files with 777 permissions
find . -type f -perm 777
```

👉 Explain why this is risky.

---

## Task B2: Log Growth Investigation

### Problem

> Find log files larger than 1MB.

```bash
find . -type f -name "*.log" -size +1M
```

---

## Task B3: Recent Incident Files

### Problem

> Find files modified in the last 1 day.

```bash
find . -type f -mtime -1
```

---

# 🔴 PART C: Structured Data Analysis (awk)

---

## Task C1: High-Value Employees

### Problem

> List employees earning more than 80k.

```bash
awk -F',' 'NR>1 && $4 > 80000 {print $2, $4}' users.csv
```

---

## Task C2: Salary Report by Role

### Problem

> Calculate total salary per role.

```bash
awk -F',' 'NR>1 {sum[$3]+=$4} END {for (r in sum) print r, sum[r]}' users.csv
```

---

# 🔴 PART D: Tool Selection (Interview Thinking)

---

## Task D1: Tool Choice (Written)

Explain **which tool you would use and why**:

1. Searching for a string in logs
2. Replacing sensitive data
3. Generating reports from CSV
4. Finding files older than 30 days

---

## Task D2: Rewrite Without Pipelines

### Problem

> Explain how you would solve Task A1 **without using a pipeline**, step-by-step.

(No commands required)

---

# 🔴 PART E: Debugging Broken Commands

---

## Task E1: Fix the Command

❌ Broken:

```bash
grep ERROR app.log | awk '{print $5}'
```

Explain:

* Why it fails
* What column is correct
* How to fix it

---

## Task E2: Dangerous Command Awareness

Explain why this is dangerous:

```bash
find / -type f -name "*.log" -delete
```



