# Incident Report: Linux Permission & Directory Traversal Failure

## 1. Executive Summary

* **Issue:** User `bob` (member of `developers`) could not access `/home/alice/project/project.txt`, despite the file granting read/write access to `developers`.

* **Root Cause:** Missing execute (`x` / traversal) permission on the parent directory `/home/alice` for non-owners.

* **Remediation:** Reassign group ownership of `/home/alice` to `developers` and set permissions to `750` (`drwxr-x---`).

* **Status:** Root cause confirmed and fix planned; final verification tests pending execution.

## 2. Environment & Initial State

### Users & Group Membership

* `chandu`: Troubleshooting operator

* `alice`: Member of `developers`

* `bob`: Member of `developers` (verified via `id bob` and `getent group developers`)

* `eve`: Member of `security`

### Path & Permissions Breakdown

| **Object** | **Permissions** | **Owner** | **Group** | **Status** |
|---|---|---|---|---|
| `/home/alice` | `drwxr-x---` (750) | `alice` | `alice` | **Blocking Point** — Bob is treated as `others` → `---` |
| `/home/alice/project` | `drwxrwxr-x` (775) | `alice` | `alice` | Traversal permitted for others (`r-x`) |
| `project.txt` | `-rw-rw-r--` (664) | `alice` | `developers` | Access permitted for `developers` (`rw-`) |

### 3. Diagnostic Investigation & Hypothesis Testing

| **#** | **Hypothesis Tested** | **Diagnostic Command** | **Result** | **Finding** |
|---:|---|---|---|---|
| 1 | Operator identity unknown | `whoami` | `chandu` | Logged-in session established |
| 2 | Bob does not exist | `id bob` | UID `1002`, groups `users`, `developers` | **Eliminated** |
| 3 | Bob not in `developers` group | `groups bob` | `bob users developers` | **Eliminated** |
| 4 | Group database mismatch | `getent group developers` | `developers:x:1004:alice,bob` | **Eliminated** |
| 5 | Error reproducibility | `ls -l /home/alice/project/project.txt` | `Permission denied` | **Confirmed reproducible** |
| 6 | File existence / sudo check | `sudo ls -l /home/alice/project/project.txt` | Success — `-rw-rw-r-- ... developers` | File exists; elevated access succeeds |
| 7 | Subdirectory blocks traversal | `sudo ls -ld /home/alice/project` | `drwxrwxr-x` | **Eliminated** — others have `r-x` |
| 8 | Parent directory blocks traversal | `ls -ld /home/alice` | `drwxr-x--- alice alice` | **CONFIRMED ROOT CAUSE** |

## 4. Technical Mechanism (Root Cause)

Linux resolves file access hierarchically:

$$
\text{/} \longrightarrow \text{home/} \longrightarrow \text{alice/} \longrightarrow \text{project/} \longrightarrow \text{project.txt}
$$

1. To open or view any file, every parent directory in the path requires execute (`x`) permission for the querying identity to allow traversal.

2. `/home/alice` had permissions `drwxr-x---` with ownership `alice:alice`.

3. Because `bob` is neither the owner (`alice`) nor in group `alice`, his access is governed by the **others** class (`---`).

4. Traversal was blocked at `/home/alice`, meaning `project.txt` permissions were never evaluated.

## 5. Remediation Plan

Apply targeted permission adjustments adhering to the principle of least privilege (avoiding open permissions like `777`):

```
# 1. Change group ownership of parent directory to developers
sudo chgrp developers /home/alice

# 2. Grant read & traversal permissions to the group, none to others
sudo chmod 750 /home/alice

```

### Resulting State

* Directory: `drwxr-x--- alice developers /home/alice`

* `alice` $\to$ `rwx`

* `developers` (`bob`, `alice`) $\to$ `r-x` (traversal enabled)

* `others` $\to$ `---`

## 6. Verification Plan & Status

### Verification Steps

1. **Inspect Directory:** `ls -ld /home/alice` (Confirm `drwxr-x--- alice developers`).

2. **Switch User:** `su - bob`

3. **Verify Read:** `cat /home/alice/project/project.txt`

4. **Verify Write:** `echo "Bob update" >> /home/alice/project/project.txt`

### Current Sign-Off Status

* \[x\] Root Cause Identified

* \[x\] Remediation Strategy Formulated

* \[x\] Fix Commands Documented

* \[ \] Post-Fix Traversal Verified (`ls -ld`)

* \[ \] Bob Read Access Verified

* \[ \] Bob Write Access Verified