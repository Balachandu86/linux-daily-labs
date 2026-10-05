# Linux Special Permissions

Linux provides three special permissions:

| Permission | Purpose |
|---|---|
| **SUID** | File owner's effective privileges |
| **SGID** | File group's effective privileges / directory group inheritance |
| **Sticky Bit** | Controls deletion in shared directories |

---

# 1. SUID

## What is SUID?

**SUID = Set User ID**

Normally, a program runs with the privileges of the user executing it.

With SUID enabled, the program runs with the **file owner's effective UID**.

```text
Normal:

User → Program → User privileges

SUID:

User → SUID Program → File owner's effective privileges
```

If the file is:

```text
Owner = root
SUID = enabled
```

a normal user can execute the program with **root's effective privileges**.

> SUID does not make the user root. Only the SUID program runs with the owner's effective privileges.

---

## How to Identify SUID

Normal permissions:

```text
-rwxr-xr-x
```

SUID enabled:

```text
-rwsr-xr-x
```

The owner's `x` changes to `s`.

### Numeric value

```text
4 = SUID
2 = SGID
1 = Sticky Bit
```

Example:

```bash
chmod 4755 file
```

Means:

```text
SUID + 755
```

---

## Real Example: `/usr/bin/passwd`

Check:

```bash
ls -l /usr/bin/passwd
```

Typical output:

```text
-rwsr-xr-x 1 root root ... /usr/bin/passwd
```

Here:

```text
root → file owner
s    → SUID enabled
```

SUID allows normal users to perform password related operations that require privileged access.

---

# 🧪 Practical Lab: SUID

## Step 1: Create a safe SUID example

Copy `id`:

```bash
cp /usr/bin/id /tmp/id-alice
```

Change ownership:

```bash
sudo chown alice:alice /tmp/id-alice
```

Check:

```bash
ls -l /tmp/id-alice
```

Expected:

```text
-rwxr-xr-x 1 alice alice ...
```

---

## Step 2: Enable SUID

```bash
sudo chmod u+s /tmp/id-alice
```

Check:

```bash
ls -l /tmp/id-alice
```

Expected:

```text
-rwsr-xr-x 1 alice alice ...
```

The important change:

```text
x → s
```

---

## Step 3: Test as Bob

Switch user:

```bash
su - bob
```

Run:

```bash
id
```

Then:

```bash
/tmp/id-alice
```

The normal `id` command shows Bob's identity.

The SUID program uses Alice as its **effective UID**.

Return:

```bash
exit
```

### Key idea

```text
Bob
 ↓
SUID program
 ↓
Effective UID = Alice
```

---

# SUID Security

Find SUID files:

```bash
find / -perm -4000 -type f 2>/dev/null
```

Security teams investigate unusual SUID programs because a vulnerable or incorrectly configured SUID program can potentially lead to **privilege escalation**.

The important question is:

> Is the SUID program necessary, trusted and securely configured?

---

# 2. Root Owned SUID

A root owned SUID executable looks like:

```text
-rwsr-xr-x 1 root root ...
```

A normal user executing it can get:

```text
uid  = normal user
euid = 0 (root)
```

Example:

```bash
sudo cp /usr/bin/id /tmp/id-root
sudo chown root:root /tmp/id-root
sudo chmod 4755 /tmp/id-root
```

Check:

```bash
ls -l /tmp/id-root
```

Run:

```bash
/tmp/id-root
```

Important output:

```text
uid=1000(chandu) ... euid=0(root)
```

Meaning:

```text
UID  → Actual user
EUID → Effective privilege used by the program
```

> A root owned SUID program is security sensitive because vulnerabilities in it may allow unintended actions with root privileges.

---

## Clean Up

```bash
sudo rm /tmp/id-root
sudo rm /tmp/id-alice
```

---

# 3. SGID

## What is SGID?

**SGID = Set Group ID**

The easiest way to remember:

```text
SUID → User / Owner
SGID → Group
```

For an executable, SGID makes the program run with the **effective group ID of the file's owning group**.

---

## SGID on Executables

Normal:

```text
-rwxr-xr-x
```

SGID:

```text
-rwxr-sr-x
```

The `s` appears in the **group execute position**.

Example:

```text
Owner = alice
Group = developers
SGID  = enabled
```

A user running the program gets:

```text
Effective group = developers
```

The user does **not** become Alice.

---

# SGID on Directories

SGID is especially useful on shared directories.

When SGID is enabled on a directory:

> New files and directories inherit the directory's group ownership.

Example:

```text
/project
Group = developers
SGID = enabled
```

If Alice creates:

```text
code.txt
```

and Bob creates:

```text
test.txt
```

both can inherit:

```text
Group = developers
```

This is useful for team collaboration.

---

# 🧪 Practical Lab: SGID Directory

## Step 1: Create directory

```bash
sudo mkdir /tmp/dev-share
```

Set group:

```bash
sudo chgrp developers /tmp/dev-share
```

Give owner and group access:

```bash
sudo chmod 770 /tmp/dev-share
```

Enable SGID:

```bash
sudo chmod g+s /tmp/dev-share
```

Check:

```bash
ls -ld /tmp/dev-share
```

Expected:

```text
drwxrws--- 2 chandu developers ...
```

The `s` indicates SGID.

---

## Step 2: Test with Alice

```bash
su - alice
```

Create a file:

```bash
touch /tmp/dev-share/alice.txt
```

Check:

```bash
ls -l /tmp/dev-share/alice.txt
```

Expected group:

```text
alice developers alice.txt
```

Even if Alice's primary group is `alice`, the file inherits:

```text
developers
```

Return:

```bash
exit
```

---

## Step 3: Test with Bob

```bash
su - bob
```

Create:

```bash
touch /tmp/dev-share/bob.txt
```

Check:

```bash
ls -l /tmp/dev-share/bob.txt
```

The group should again be:

```text
developers
```

---

# SUID vs SGID

| Permission | Effect |
|---|---|
| **SUID** | Executable uses file owner's effective UID |
| **SGID on executable** | Executable uses file group's effective GID |
| **SGID on directory** | New files inherit directory's group |

Remember:

```text
SUID → Owner identity
SGID → Group identity
```

---

# 4. Sticky Bit

The easiest way to remember:

```text
SUID        → Owner privilege
SGID        → Group privilege / inheritance
Sticky Bit  → Controls deletion
```

The Sticky Bit is commonly used on:

```text
/tmp
```

Check:

```bash
ls -ld /tmp
```

Typical output:

```text
drwxrwxrwt
```

The final `t` means Sticky Bit is enabled.

---

## What does Sticky Bit do?

Consider a shared directory:

```text
drwxrwxrwx shared/
```

Everyone can write to it.

Without Sticky Bit:

```text
Alice creates alice.txt
Bob may delete alice.txt
```

With Sticky Bit:

```text
drwxrwxrwt shared/
```

Users can generally delete or rename only files they own.

Example:

```text
Alice → creates alice.txt
Bob   → creates bob.txt

Bob   → delete bob.txt     ✅
Bob   → delete alice.txt   ❌
Alice → delete alice.txt   ✅
```

This is why `/tmp` uses the Sticky Bit.

---

# 🧪 Practical Lab: Sticky Bit

## Step 1: Create directory

```bash
sudo mkdir /tmp/sticky-lab
```

Give everyone access:

```bash
sudo chmod 777 /tmp/sticky-lab
```

Check:

```bash
ls -ld /tmp/sticky-lab
```

Expected:

```text
drwxrwxrwx
```

---

## Step 2: Enable Sticky Bit

```bash
sudo chmod +t /tmp/sticky-lab
```

Check:

```bash
ls -ld /tmp/sticky-lab
```

Expected:

```text
drwxrwxrwt
```

The final `t` indicates Sticky Bit.

---

## Step 3: Test with Alice

```bash
su - alice
```

Create:

```bash
touch /tmp/sticky-lab/alice.txt
```

Check:

```bash
ls -l /tmp/sticky-lab
```

Return:

```bash
exit
```

---

## Step 4: Test with Bob

```bash
su - bob
```

Check:

```bash
ls -l /tmp/sticky-lab
```

Bob can see Alice's file.

Try deleting it:

```bash
rm /tmp/sticky-lab/alice.txt
```

Expected:

```text
Operation not permitted
```

Why?

Because Bob does not own `alice.txt`, and the Sticky Bit protects the directory entries.

---

# Quick Revision

```text
SUID
 ↓
File owner's effective UID

SGID
 ↓
File group's effective GID
or
Directory group inheritance

Sticky Bit
 ↓
Controls deletion in shared directories
```

### Permission Symbols

```text
SUID       → s in owner execute position
SGID       → s in group execute position
Sticky Bit → t in others execute position
```

### Numeric Values

```text
SUID        = 4
SGID        = 2
Sticky Bit  = 1
```

Example:

```bash
chmod 4755 file
```

```text
4 → SUID
755 → normal permissions
```

---

# Security Perspective

Special permissions are not automatically dangerous.

The key security questions are:

1. **Who owns the file?**
2. **Which special permission is enabled?**
3. **Is the permission necessary?**
4. **Is the program trusted and secure?**
5. **Could a vulnerability allow privilege escalation?**

Useful commands:

```bash
ls -l file
```

```bash
ls -ld directory
```

```bash
find / -perm -4000 -type f 2>/dev/null
```

```bash
find / -perm -2000 -type f 2>/dev/null
```

These concepts are important when auditing Linux systems for **privilege escalation and incorrect permissions**.