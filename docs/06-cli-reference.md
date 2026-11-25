# Specification 06: Direct CLI Command Reference

[← Back to Documentation Index](README.md)

---

## 1. Overview & Paradigm

For automated scripts, cron jobs, and terminal power users, **Python File Organizer** exposes direct Unix-style subcommands that execute without interactive prompts.

```text
python-file-organizer [COMMAND] [OPTIONS]
```

---

## 2. Command Reference

### 2.1 `duplicates`
Finds bit-for-bit duplicate files using 3-stage cascaded hashing and moves duplicate copies to the OS Trash.

```bash
python-file-organizer duplicates [PATH] [OPTIONS]
```

* **Arguments:**
  * `PATH`: Directory to scan (Defaults to current working directory `.`).
* **Options:**
  * `-r, --recursive`: Scan all subdirectories recursively.
  * `--dry-run`: Preview duplicate files and reclaimable space without moving anything to Trash.
  * `-y, --yes`: Bypass interactive confirmation prompt.
  * `--min-size <BYTES>`: Ignore files smaller than threshold (e.g. 1024).

#### Examples:
```bash
# Preview duplicates in Downloads folder without deleting
python-file-organizer duplicates ~/Downloads --dry-run

# Recursively scan and trash duplicate copies with confirmation bypassed
python-file-organizer duplicates ~/Pictures --recursive -y
```

---

### 2.2 `organize`
Sorts files from the target directory into organized subfolders according to chosen criteria.

```bash
python-file-organizer organize [PATH] [OPTIONS]
```

* **Arguments:**
  * `PATH`: Directory to organize (Defaults to current working directory `.`).
* **Options:**
  * `--by [type|date|size]`: Organization criteria (Default: `type`).
  * `--dest <DIR>`: Separate destination folder (Default: inside target directory).
  * `--conflict [rename|skip|overwrite]`: Collision policy for existing files (Default: `rename`).
  * `--clean-empty`: Prunes empty folders left behind after organization.
  * `--dry-run`: Simulates folder creation and file movements without modifying disk.
  * `-y, --yes`: Bypass confirmation.

#### Examples:
```bash
# Organize by file type in dry-run mode
python-file-organizer organize ~/Downloads --by type --dry-run

# Organize photos by Date (YYYY/MM) and clean empty folders
python-file-organizer organize ~/Photos --by date --clean-empty -y

# Organize by file size into Tiny, Small, Medium, Large buckets
python-file-organizer organize ~/Desktop --by size
```

---

### 2.3 `undo`
Rolls back the most recent organization operation, restoring all moved files to their previous paths.

```bash
python-file-organizer undo [OPTIONS]
```

* **Options:**
  * `--path <DIR>`: Directory containing `.organizer_history.json` (Default: `.`).
  * `-y, --yes`: Bypass confirmation.

#### Example:
```bash
python-file-organizer undo
```

---

### 2.4 `interactive`
Explicitly launches the full guided Terminal User Interface wizard (identical to running `python-file-organizer` with no arguments).

```bash
python-file-organizer interactive
```

---

## 3. Standard POSIX Exit Codes

| Exit Code | Meaning |
| :---: | :--- |
| **`0`** | **Success:** Operation completed normally. |
| **`1`** | **Runtime Error:** I/O failure, permission denied, or missing history journal. |
| **`2`** | **CLI Syntax Error:** Invalid flag or unrecognized argument passed. |
| **`130`** | **Process Interrupted:** Interrupted via `Ctrl+C` (SIGINT) with safe state committed. |

---

## 4. Automation & Scripting Example (Cron / Shell)

### Daily Downloads Auto-Cleanup Script:
```bash
#!/usr/bin/env bash
set -euo pipefail

# 1. Purge duplicate downloads older than 7 days
python-file-organizer duplicates ~/Downloads --recursive -y

# 2. Sort remaining files by extension type and prune empty folders
python-file-organizer organize ~/Downloads --by type --clean-empty -y
```
