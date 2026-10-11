## 1. `find` — Search the Filesystem

`find` searches the actual directory tree and supports filters such as name and file type.

```bash
find /etc -name "ssh_config"
find /tmp -type f
find /home -type d
find /tmp -name "*.txt"
```

- `-name` — Match a filename (case-sensitive).
- `-type f` — Regular files only.
- `-type d` — Directories only.
- `"*.txt"` — Names ending in `.txt`.

## 2. `locate` — Search Indexed Paths

`locate` searches a database of file paths. It is usually faster than `find`, but results may be outdated.

```bash
locate ssh_config
locate passwd
locate "*.conf"
```

Check whether it is installed:

```bash
command -v locate
```

On Ubuntu, install it if needed:

```bash
sudo apt update
sudo apt install plocate
```

Refresh the database and search again:

```bash
sudo updatedb
locate app.conf
```

**Remember:** New files may not appear in `locate` until its database is updated.

## 3. `which` — Find an Executable in `PATH`

`which` shows the executable selected through the shell's `PATH`.

```bash
which python3
which ls
which grep
which bash
echo "$PATH"
```

Example output:

```text
/usr/bin/ls
```

`which` is for command lookup, not general file searches.

## 4. `whereis` — Find Command-Related Files

`whereis` searches known locations for a command's binary, source files, and manual pages.

```bash
whereis ls
whereis python3
whereis ssh
```

Example output (varies by system):

```text
ls: /usr/bin/ls /usr/share/man/man1/ls.1.gz
```

## Quick Comparison

| Command | Best use |
|---|---|
| `find` | Search the filesystem using filters |
| `locate` | Quickly search indexed paths |
| `which` | Find the executable resolved through `PATH` |
| `whereis` | Find command binaries, source, and manual pages |

---

# Practical Lab: Finding File Locations

**Objective:** Practise `find`, `locate`, `which`, and `whereis`.

## Lab 1: Create Test Files

```bash
mkdir -p ~/file-search-lab/config
mkdir -p ~/file-search-lab/logs

touch ~/file-search-lab/config/app.conf
touch ~/file-search-lab/config/ssh_config
touch ~/file-search-lab/logs/auth.log
touch ~/file-search-lab/notes.txt
```

View the structure:

```bash
tree ~/file-search-lab
```

Expected:

```text
file-search-lab/
├── config
│   ├── app.conf
│   └── ssh_config
├── logs
│   └── auth.log
└── notes.txt
```

## Lab 2: Practise `find`

Find `app.conf`:

```bash
find ~/file-search-lab -name "app.conf"
```

Expected path (your home directory may differ):

```text
/home/chandu/file-search-lab/config/app.conf
```

Find all `.conf` files:

```bash
find ~/file-search-lab -name "*.conf"
```

Find directories:

```bash
find ~/file-search-lab -type d
```

Find files modified within the last 24 hours:

```bash
find ~/file-search-lab -type f -mtime -1
```

## Lab 3: Practise `locate`

Check/install `locate` if necessary, then update its database:

```bash
command -v locate
sudo apt update
sudo apt install plocate
sudo updatedb
```

Search for the sample files:

```bash
locate app.conf
locate auth.log
locate file-search-lab
```

Compare `find` with `locate`:

```bash
touch ~/file-search-lab/newfile.txt

find ~/file-search-lab -name "newfile.txt"
locate newfile.txt
```

If `locate` does not show the new file, update the database and retry:

```bash
sudo updatedb
locate newfile.txt
```

**Observation:** `find` searches the directory tree directly; `locate` searches its indexed database.

## Lab 4: Practise `which`

```bash
which ls
which grep
which python3
which bash
echo "$PATH"
```

**Observation:** `which` identifies the executable found through `PATH`.

## Lab 5: Practise `whereis`

```bash
whereis ls
whereis grep
whereis python3
whereis bash
```

Compare:

```bash
which ls
whereis ls
```

**Observation:** `which` focuses on the executable resolved through `PATH`; `whereis` can also show related manual and source locations.

---

## Cybersecurity Use Case

Use file-search commands to locate configuration files, logs, and files that may need security review.

**Quick revision:**

```text
find     → Search actual filesystem
locate   → Search indexed paths
which    → Find command through PATH
whereis  → Find command-related files
```
'''
out = Path("/mnt/data/linux-file-search-commands-summary.md")
out.write_text(markdown, encoding="utf-8")
print(f"Created {out}")
