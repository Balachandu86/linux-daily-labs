## 1. `cat` — Display and Combine Files

Display a file:

```bash
cat file.txt
```

Show line numbers:

```bash
cat -n file.txt
```

Display multiple files sequentially:

```bash
cat file1.txt file2.txt
```

Combine files into one file:

```bash
cat file1.txt file2.txt > combined.txt
```

This overwrites `combined.txt` if it already exists.

> `cat` displays or combines file contents; it is not an interactive editor.

---

## 2. `nano` — Beginner-Friendly Text Editor

Open or create a file:

```bash
nano notes.txt
```

Enter text, for example:

```text
Linux file editing practice
Learning Linux for cybersecurity
```

### Essential shortcuts

| Shortcut | Action |
|---|---|
| `Ctrl + O` | Save |
| `Enter` | Confirm filename |
| `Ctrl + X` | Exit |
| `Ctrl + W` | Search |
| `Ctrl + K` | Cut current line |
| `Ctrl + U` | Paste cut text |

Verify your saved file:

```bash
cat notes.txt
```

**Expected result:** The text you entered is displayed.

**Use case:** Edit configuration files, scripts, and lab notes directly in the terminal.

---

## 3. `vim` — Modal Text Editor

Open or create a file:

```bash
vim notes.txt
```

### Basic editing workflow

1. Press `i` to enter Insert mode.
2. Type your text.
3. Press `Esc` to return to Normal mode.
4. Type `:wq` and press `Enter` to save and quit.

### Essential commands

| Command | Action |
|---|---|
| `i` | Enter Insert mode |
| `Esc` | Return to Normal mode |
| `:w` | Save |
| `:q` | Quit |
| `:wq` | Save and quit |
| `:q!` | Quit without saving changes |
| `dd` | Delete current line |
| `yy` | Copy current line |
| `p` | Paste after cursor |
| `/word` | Search for `word` |

Verify the changes:

```bash
cat notes.txt
```

**Remember:** `i` to edit, `Esc` to stop editing, `:wq` to save and exit.

---

## 4. `tee` — Display and Save Output

`tee` displays command output in the terminal and writes the same output to a file.

### Lab 1: Save command output

```bash
ls -l /etc | tee file-list.txt
```

**Expected result:** The `/etc` listing appears in the terminal and is saved to `file-list.txt`.

Verify:

```bash
cat file-list.txt
```

### Lab 2: Append output

```bash
date | tee -a file-list.txt
```

`-a` appends the date to the file instead of overwriting it.

### Lab 3: Write to a privileged file

```bash
echo "Test entry" | sudo tee /tmp/test-log.txt
```

This demonstrates using `sudo tee` to write to a file that requires administrator permissions.

---

## Quick Comparison

| Command | Purpose |
|---|---|
| `cat` | Display or combine file contents |
| `nano` | Edit files with simple keyboard shortcuts |
| `vim` | Edit files using modes and commands |
| `tee` | Display output and save it simultaneously |
| `>` | Redirect output to a file, overwriting it |
| `>>` | Append output to a file |
| `tee -a` | Display output and append it to a file |

## Cybersecurity Use Case

Use these commands to edit configuration files, maintain scripts and lab notes, and capture diagnostic output during system checks or investigations.
'''
out = Path("/mnt/data/linux-text-file-editing-summary.md")
out.write_text(markdown, encoding="utf-8")
print(f"Created: {out}")
