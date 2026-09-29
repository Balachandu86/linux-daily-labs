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
