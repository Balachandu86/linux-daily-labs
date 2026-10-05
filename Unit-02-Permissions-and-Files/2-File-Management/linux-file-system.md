# Lesson: Understanding the Linux Filesystem

## 1. Linux Filesystem

Linux uses a hierarchical filesystem structure starting from the root directory `/`.

Unlike Windows, Linux organizes files and directories under a single root directory.

## 2. Important Linux Directories

| Directory | Use                               |
| --------- | --------------------------------- |
| `/`       | Root of the entire filesystem     |
| `/home`   | Home directories of regular users |
| `/root`   | Home directory of the root user   |
| `/etc`    | System configuration files        |
| `/bin`    | Essential user commands           |
| `/sbin`   | System administration commands    |
| `/usr`    | Applications and libraries        |
| `/var`    | Logs, cache, and variable data    |
| `/tmp`    | Temporary files                   |
| `/dev`    | Device files                      |
| `/proc`   | Process and kernel information    |
| `/boot`   | Bootloader and kernel files       |
| `/opt`    | Optional third party applications |
| `/mnt`    | Temporary filesystem mount point  |
| `/media`  | Removable media mount points      |

## 3. Filesystem Navigation Commands

| Command    | Use                            |
| ---------- | ------------------------------ |
| `pwd`      | Displays current directory     |
| `ls`       | Lists files and directories    |
| `ls -l`    | Displays detailed listing      |
| `ls -a`    | Shows hidden files             |
| `cd /home` | Changes to `/home`             |
| `cd ..`    | Moves to parent directory      |
| `cd ~`     | Moves to user's home directory |
| `cd /`     | Moves to root directory        |

## Practice

```bash
pwd
ls /
ls -l /home
cd /etc
pwd
cd ..
cd ~
```

**Objective:** Understand the Linux directory structure and navigate the filesystem using basic commands.

# Linux Links and File Information

## 1. Hard Link vs Symbolic Link

### Concept

A **hard link** is another directory entry pointing to the **same inode/data**.

A **symbolic link** is a separate file that points to the **path of another file**.

```text
Hard Link

file.txt ──┐
           ├── same inode → data
hard.txt ──┘


Symbolic Link

file.txt → inode → data
soft.txt → "file.txt"
```

### 🧪 Practical Lab

Create a test directory:

```bash
mkdir /tmp/link-lab
cd /tmp/link-lab
```

Create a file:

```bash
echo "Hello Linux" > original.txt
```

Create a hard link:

```bash
ln original.txt hard.txt
```

Create a symbolic link:

```bash
ln -s original.txt soft.txt
```

Check inode numbers:

```bash
ls -li
```

Expected:

```text
original.txt → same inode
hard.txt     → same inode
soft.txt     → different inode
```

The hard link and original are two names for the same underlying file.

---

### Important Test

Delete the original:

```bash
rm original.txt
```

Test the hard link:

```bash
cat hard.txt
```

✅ Still works.

Test the symbolic link:

```bash
cat soft.txt
```

❌ Fails because `soft.txt` points to `original.txt`, which no longer exists.

---

### Quick Comparison

| Hard Link | Symbolic Link |
|---|---|
| Same inode | Different inode |
| Points to file data | Points to a pathname |
| Usually cannot cross filesystems | Can cross filesystems |
| Survives deletion of original name | Can become broken |
| `ln file link` | `ln -s file link` |

> **Remember:** Hard link → same inode. Symbolic link → path reference.

---

# 2. `file` vs `stat`

Both commands are useful for understanding files, but they provide different information.

## `file`

`file` tells you **what type of file something is**.

Example:

```bash
file /tmp/link-lab/hard.txt
```

```bash
file /tmp/link-lab/soft.txt
```

The symbolic link is identified as a **symbolic link**.

You can also check:

```bash
file /bin/ls
```

This provides information about the executable, such as its type and architecture.

---

## `stat`

`stat` provides **detailed file metadata**.

Example:

```bash
stat /tmp/link-lab/hard.txt
```

It can show:

```text
File
Size
Blocks
IO Block
Device
Inode
Links
Access
Uid
Gid
Access time
Modify time
Change time
```

---

## Quick Difference

```text
file → What is this?

stat → What are this file's details?
```

| Command | Purpose |
|---|---|
| `file` | Identify file type |
| `stat` | Display detailed metadata |