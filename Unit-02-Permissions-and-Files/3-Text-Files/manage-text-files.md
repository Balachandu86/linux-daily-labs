## 1. Archive Files with `tar`

`tar` combines files and directories into an archive. It does **not** compress by default.

```bash
tar -cvf backup.tar file1.txt file2.txt
```

Extract an archive:

```bash
tar -xvf backup.tar
```

List archive contents without extracting:

```bash
tar -tvf backup.tar
```

Create a gzip-compressed archive:

```bash
tar -czvf backup.tar.gz myfolder/
```

| Option | Meaning |
|---|---|
| `-c` | Create archive |
| `-x` | Extract archive |
| `-t` | List contents |
| `-v` | Show filenames |
| `-f` | Specify archive file |
| `-z` | Use gzip compression |
| `-j` | Use bzip2 compression |
| `-J` | Use xz compression |

## 2. Archive Files with `cpio`

`cpio` creates or extracts archives using a list of filenames, often supplied by another command.

Create an archive:

```bash
find myfolder -type f | cpio -ov > backup.cpio
```

Extract it:

```bash
cpio -iv < backup.cpio
```

- `-o` — Create archive
- `-i` — Extract archive
- `-v` — Display filenames

## 3. Copy Raw Data with `dd`

`dd` copies data in blocks and can be used for disk images and other low-level operations.

```bash
dd if=input.img of=copy.img bs=4M status=progress
```

- `if` — Input file
- `of` — Output file
- `bs` — Block size

> **Caution:** Choosing the wrong output file or device can destroy data. Verify `if` and `of` carefully before running `dd`.

## 4. Compress and Decompress Files

| Tool | Compress | Decompress |
|---|---|---|
| `gzip` | `gzip file.txt` | `gunzip file.txt.gz` |
| `bzip2` | `bzip2 file.txt` | `bunzip2 file.txt.bz2` |
| `xz` | `xz file.txt` | `unxz file.txt.xz` |
| `zip` | `zip files.zip file1.txt` | `unzip files.zip` |

Install `zip` if needed. These compression commands usually remove the original input file after creating the compressed file, unless options are used to keep it.

---

## 5. Verify File Integrity with Checksums

A checksum helps detect whether file contents have changed.

Generate a SHA-256 checksum:

```bash
sha256sum file.txt
```

Save the checksum in a file:

```bash
sha256sum file.txt > checksum.txt
```

Verify later from the same directory:

```bash
sha256sum -c checksum.txt
```

Expected result if `file.txt` is unchanged:

```text
file.txt: OK
```

Other commands include `md5sum` and `sha512sum`. For security-sensitive integrity checks, prefer SHA-256 or SHA-512 over MD5.

---

# Text Processing Commands

## 6. `sort` — Sort Lines

```bash
sort names.txt
sort -n numbers.txt
sort -r names.txt
```

- Default — Alphabetical order
- `-n` — Numeric order
- `-r` — Reverse order

## 7. `uniq` — Find Adjacent Duplicate Lines

```bash
uniq names.txt
sort names.txt | uniq
sort names.txt | uniq -c
```

`uniq` removes adjacent duplicate lines. Sort first to group duplicates; `-c` counts occurrences.

## 8. `wc` — Count Content

```bash
wc file.txt
wc -l file.txt
wc -w file.txt
wc -c file.txt
```

| Option | Counts |
|---|---|
| Default | Lines, words, and bytes |
| `-l` | Lines |
| `-w` | Words |
| `-c` | Bytes |
| `-m` | Characters |

## 9. `cut` — Extract Fields or Characters

Extract usernames from `/etc/passwd`:

```bash
cut -d ':' -f 1 /etc/passwd
```

Extract the first five characters of each line:

```bash
cut -c 1-5 file.txt
```

- `-d` — Field delimiter
- `-f` — Field number(s)
- `-c` — Character positions

## 10. `tr` — Translate or Delete Characters

Convert lowercase letters to uppercase:

```bash
echo "hello" | tr 'a-z' 'A-Z'
```

Expected output:

```text
HELLO
```

Remove digits:

```bash
echo "hello123" | tr -d '0-9'
```

Expected output:

```text
hello
```

`-d` deletes the specified characters.

## 11. `sed` — Find and Replace Text

Replace the first occurrence of `error` on each line in the output:

```bash
sed 's/error/warning/' file.txt
```

Replace all occurrences on each line:

```bash
sed 's/error/warning/g' file.txt
```

Modify the file directly:

```bash
sed -i 's/error/warning/g' file.txt
```

> **Note:** `-i` changes the file itself. Make a backup first if needed.

## 12. `awk` — Process Structured Text

Print the first field:

```bash
awk '{print $1}' file.txt
```

Print the first and third fields:

```bash
awk '{print $1, $3}' file.txt
```

Extract usernames from `/etc/passwd`:

```bash
awk -F ':' '{print $1}' /etc/passwd
```

`-F` specifies the field separator.

---

## Quick Revision

| Command | Main purpose |
|---|---|
| `tar` | Create, list, and extract archives |
| `cpio` | Archive files from a list |
| `dd` | Copy raw data in blocks |
| `gzip`, `bzip2`, `xz`, `zip` | Compress and decompress |
| `sha256sum` | Generate and verify checksums |
| `sort` | Sort lines |
| `uniq` | Handle adjacent duplicate lines |
| `wc` | Count lines, words, bytes, or characters |
| `cut` | Extract fields or characters |
| `tr` | Translate or delete characters |
| `sed` | Transform text using patterns |
| `awk` | Process fields and structured text |

## Cybersecurity Practice

For log analysis and system investigations, focus especially on `sort`, `uniq`, `sed`, and `awk`, along with text search tools such as `grep`. Use checksums to check file integrity and archive commands to package files for storage or transfer.
'''
out = Path("/mnt/data/linux-file-management-text-processing.md")
out.write_text(markdown, encoding="utf-8")
print(f"Created: {out}")
