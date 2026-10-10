from pathlib import Path

markdown = r'''# Linux File Management and Command-Line Essentials

## 1. File and Directory Management

### `mkdir` — Create directories

```bash
mkdir /tmp/file-lab
ls -ld /tmp/file-lab
```

Create nested directories, including missing parents:

```bash
mkdir -p /tmp/file-lab/a/b/c
```

### `touch` — Create an empty file

```bash
touch /tmp/file-lab/test.txt
ls -l /tmp/file-lab/test.txt
```

**Note:** If the file already exists, `touch` updates its timestamps.

### `cp` — Copy files and directories

```bash
cp /tmp/file-lab/test.txt /tmp/file-lab/test-copy.txt
cp -r /tmp/file-lab /tmp/file-lab-backup
```

`-r` copies a directory and its contents recursively.

### `mv` — Move or rename

```bash
mv /tmp/file-lab/test.txt /tmp/file-lab/renamed.txt
mv /tmp/file-lab/renamed.txt /tmp/
```

Use `mv` to rename or move files and directories.

### `rm` and `rmdir` — Remove files and directories

```bash
rm /tmp/test.txt
rm -r /tmp/file-lab
rm -f filename
rm -rf directory
rmdir empty-directory
```

- `rm` removes files.
- `rm -r` removes a directory and its contents.
- `rm -f` forces removal without prompting.
- `rm -rf` combines recursive and force removal.
- `rmdir` removes an empty directory only.

> **Caution:** `rm -rf` can permanently delete data. Check the path before running it.

### 🧪 Practical Lab: Basic File Operations

Run the commands in a test directory:

```bash
mkdir /tmp/file-lab
cd /tmp/file-lab

touch file1.txt
mkdir dir1
cp file1.txt file2.txt
mv file2.txt dir1/

ls -l
ls -l dir1/
```

Expected: `file1.txt` is in the current directory and `file2.txt` is inside `dir1/`.

Clean up:

```bash
rm dir1/file2.txt
rmdir dir1
```

`rmdir` works because `dir1` is now empty.

---

## 2. Navigation and Directory Structure

### `pwd` — Show current directory

```bash
pwd
```

Example output:

```text
/home/chandu
```

### `tree` — Display directory structure

```bash
tree /tmp/file-lab
tree -L 2 /tmp/file-lab
```

`-L 2` limits the display to two directory levels.

If `tree` is not installed:

```bash
sudo apt install tree
```

---

## 3. Reading Files

### `less` and `more` — Read files page by page

```bash
less /var/log/syslog
more /var/log/syslog
```

`less` provides more navigation features and is commonly preferred.

Useful `less` keys:

| Key | Action |
|---|---|
| `Space` | Next page |
| `b` | Previous page |
| `↑` / `↓` | Move through lines |
| `/word` | Search for `word` |
| `q` | Quit |

### `head` — Show the beginning

```bash
head file.txt
head -n 5 file.txt
head -n 20 file.txt
```

By default, `head` displays the first 10 lines.

### `tail` — Show the end

```bash
tail file.txt
tail -n 5 file.txt
```

By default, `tail` displays the last 10 lines.

Follow new log entries in real time:

```bash
tail -f /var/log/syslog
```

`-f` keeps watching the file as new lines are added. Press `Ctrl+C` to stop.

---

## 4. `grep` — Search Text

`grep` searches files for lines matching a pattern.

```bash
grep "error" logfile.txt
```

### Useful options

| Command | Purpose |
|---|---|
| `grep -i "error" file` | Case-insensitive search |
| `grep -n "error" file` | Show matching line numbers |
| `grep -v "error" file` | Show lines that do not match |
| `grep -r "password" /etc/` | Search recursively |
| `grep -w "root" file` | Match the whole word |
| `grep -c "error" file` | Count matching lines |
| `grep -in "error" file` | Ignore case and show line numbers |

### 🧪 Practical Lab: Find Authentication Failures

```bash
grep -i "failed" /var/log/auth.log
```

This searches for authentication failure messages without case sensitivity. The log path and available entries depend on the Linux distribution and system configuration.

---

## 5. Redirection

Linux commands use three standard streams:

| Stream | Number | Purpose |
|---|---:|---|
| Standard input (`stdin`) | `0` | Input to a command |
| Standard output (`stdout`) | `1` | Normal command output |
| Standard error (`stderr`) | `2` | Error messages |

### Redirect output

```bash
ls > output.txt
```

Writes standard output to a file, **overwriting** its previous contents.

```bash
ls >> output.txt
```

Appends standard output to the file.

### Redirect input

```bash
sort < names.txt
```

Uses `names.txt` as the command's input.

### Redirect errors

```bash
ls /does-not-exist 2> error.txt
```

Sends error messages to `error.txt`.

### Redirect both output streams

```bash
command > output.txt 2> error.txt
command > all.txt 2>&1
```

The second command sends both standard output and standard error to `all.txt`.

---

## 6. Command Chaining and Pipes

### `;` — Run the next command regardless of success

```bash
mkdir test; echo "Done"
```

The second command runs even if the first command fails.

### `&&` — Run only if the previous command succeeds

```bash
mkdir test && echo "Created"
```

### `||` — Run only if the previous command fails

```bash
mkdir test || echo "Failed"
```

### `|` — Pipe output into another command

```bash
ls /etc | grep ssh
ps aux | grep nginx
```

A pipe sends the first command's standard output to the next command's standard input.

### `!` — Negate the exit status

```bash
! grep "root" file.txt
```

`!` reverses the command's exit status:

- Exit status `0` (success) becomes nonzero (failure).
- Nonzero exit status (failure) becomes `0` (success).

It does **not** reverse or hide the command's displayed output.

---

## 7. `xargs` — Convert Input into Arguments

`xargs` reads items from standard input and passes them as arguments to another command.

### Example

```bash
echo "file1.txt file2.txt" | xargs ls -l
```

Conceptually, this runs:

```bash
ls -l file1.txt file2.txt
```

### 🧪 Practical Lab

List `.txt` files found under a directory:

```bash
find /tmp/file-lab -name "*.txt" | xargs ls -l
```

Run one command per input item:

```bash
printf "one\ntwo\nthree\n" | xargs -n 1 echo
```

Expected output:

```text
one
two
three
```

### Safer handling of filenames

Plain `xargs` can mishandle filenames containing spaces or special characters. Use null-delimited input with `find`:

```bash
find . -name "*.txt" -print0 | xargs -0 ls -l
```

`-print0` and `-0` preserve filenames containing spaces and other characters that would otherwise be treated as separators.

---

## Quick Revision

| Command / Operator | Purpose |
|---|---|
| `mkdir` | Create directories |
| `touch` | Create files or update timestamps |
| `cp` | Copy files/directories |
| `mv` | Move or rename |
| `rm` / `rmdir` | Remove files / empty directories |
| `pwd` | Show current directory |
| `tree` | Display directory structure |
| `less` / `more` | Read files page by page |
| `head` / `tail` | Show beginning / end of a file |
| `tail -f` | Follow new log entries |
| `grep` | Search text |
| `>` / `>>` | Overwrite / append standard output |
| `<` | Redirect standard input |
| `2>` | Redirect standard error |
| `\|` | Pipe output to another command |
| `;` | Run next command regardless of success |
| `&&` | Run next command on success |
| `\|\|` | Run next command on failure |
| `!` | Negate exit status |
| `xargs` | Convert input into command arguments |

**Practice workflow:** Create files → inspect directories → search logs → redirect output → combine commands.
'''

output_path = Path("/mnt/data/linux-file-management-commands-summary.md")
output_path.write_text(markdown, encoding="utf-8")
print(f"Created: {output_path}")
