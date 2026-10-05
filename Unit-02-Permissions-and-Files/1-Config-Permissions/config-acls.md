# Linux ACLs: Access Control Lists

## 1. What are ACLs?

**ACL = Access Control List**

Normal Linux permissions provide only:

```text
Owner  → rwx
Group  → rwx
Others → rwx
```

This becomes limiting when different users need different permissions on the same file.

### Example

For `alice.txt`:

```text
Alice → rw
Bob   → rw
Eve   → r
```

Standard permissions cannot easily provide these individual permissions.

**ACLs allow permissions to be assigned to specific users or groups.**

```text
alice → rw
bob   → rw
eve   → r
```

---

# 2. Important ACL Commands

| Command | Purpose |
|---|---|
| `getfacl` | View ACLs |
| `setfacl` | Create or modify ACLs |

Remember:

```text
getfacl → View
setfacl → Modify
```

---

# 3. 🧪 Lab 1: Check Current ACL

Create a test directory and file:

```bash
sudo mkdir /tmp/acl-lab
sudo touch /tmp/acl-lab/test.txt
```

View the ACL:

```bash
getfacl /tmp/acl-lab/test.txt
```

Expected:

```text
# file: test.txt
# owner: root
# group: root
user::rw-
group::rw-
other::r--
```

These entries represent the normal Linux permissions through the ACL interface.

---

# 4. 🧪 Lab 2: Give Bob Read/Write Access

Give Bob specific permissions:

```bash
sudo setfacl -m u:bob:rw /tmp/acl-lab/test.txt
```

Check:

```bash
getfacl /tmp/acl-lab/test.txt
```

Expected:

```text
user::rw-
user:bob:rw-
group::rw-
mask::rw-
other::r--
```

Important entry:

```text
user:bob:rw-
```

Bob now has `read + write` access through an ACL.

---

# 5. ACL Mask

The **ACL mask** controls the effective permissions of:

* Named users
* Named groups
* Owning group

It does **not** affect:

* File owner
* Others

Example:

```text
Bob ACL     → rw-
ACL mask    → rw-
Effective   → rw-
```

So Bob can read and write.

---

# 6. 🧪 Lab 3: Prove Bob Can Write

Run:

```bash
sudo -u bob sh -c 'echo "Written by Bob" >> /tmp/acl-lab/test.txt'
```

Check:

```bash
cat /tmp/acl-lab/test.txt
```

Expected:

```text
Written by Bob
```

Check the file:

```bash
ls -l /tmp/acl-lab/test.txt
```

You may see:

```text
-rw-rw-r--+ ...
```

The `+` means the file has **extended ACL entries**.

---

# 7. 🧪 Lab 4: Give Eve Read Only

Give Eve read permission:

```bash
sudo setfacl -m u:eve:r /tmp/acl-lab/test.txt
```

Check:

```bash
getfacl /tmp/acl-lab/test.txt
```

Expected:

```text
user::rw-
user:bob:rw-
user:eve:r--
group::r--
mask::rw-
other::r--
```

### Test Eve

Read:

```bash
sudo -u eve cat /tmp/acl-lab/test.txt
```

Expected:

```text
Works
```

Try writing:

```bash
sudo -u eve sh -c 'echo "Eve was here" >> /tmp/acl-lab/test.txt'
```

Expected:

```text
Permission denied
```

### Test Bob

```bash
sudo -u bob sh -c 'echo "Bob was here" >> /tmp/acl-lab/test.txt'
```

Expected:

```text
Works
```

Final permissions:

```text
root   → rw
bob    → rw
eve    → r
others → r
```

### Key Advantage

ACLs allow different permissions for individual users **without changing the file owner or owning group**.

---

# 8. 🧪 Lab 5: Understand the ACL Mask

Change the mask:

```bash
sudo setfacl -m m:r /tmp/acl-lab/test.txt
```

Check:

```bash
getfacl /tmp/acl-lab/test.txt
```

You should see:

```text
user:bob:rw-        #effective:r--
user:eve:r--
mask::r--
```

Bob's ACL still says:

```text
rw-
```

But the mask limits the effective permission:

```text
ACL permission → rw-
Mask           → r--
Effective      → r--
```

### Restore the mask

```bash
sudo setfacl -m m:rw /tmp/acl-lab/test.txt
```

> **Interview point:** The ACL entry shows the requested permission, while the mask can restrict the effective permission.

---

# 9. Important ACL Commands

### View ACL

```bash
getfacl file
```

### Add or modify a user ACL

```bash
setfacl -m u:bob:rw file
```

### Remove a specific user ACL

```bash
setfacl -x u:bob file
```

### Remove all extended ACL entries

```bash
setfacl -b file
```

### Basic workflow

```text
setfacl
   ↓
getfacl
   ↓
Test permissions
```

---

# 10. 🧪 Lab 6: ACLs on Directories

ACLs can also control access to shared directories.

Create the directory:

```bash
sudo mkdir /tmp/acl-share
sudo chmod 700 /tmp/acl-share
```

Give Bob full access:

```bash
sudo setfacl -m u:bob:rwx /tmp/acl-share
```

Give Eve read and traversal access:

```bash
sudo setfacl -m u:eve:rx /tmp/acl-share
```

Check:

```bash
getfacl /tmp/acl-share
```

Expected:

```text
user::rwx
user:bob:rwx
user:eve:r-x
mask::rwx
other::---
```

---

# 11. Directory Permissions

For directories:

| Permission | Meaning |
|---|---|
| `r` | List contents |
| `w` | Create, delete, rename entries |
| `x` | Enter / traverse directory |

Therefore:

```text
Bob   → rwx → Full access
Eve   → r-x → Enter and read, but cannot create/delete
Others → --- → No access
```

---

# Quick Revision

```text
ACL
 ↓
Allows user/group specific permissions

getfacl
 ↓
View ACL

setfacl
 ↓
Modify ACL

ACL Mask
 ↓
Limits effective permissions

+
 ↓
Extended ACL exists
```

### Core Example

```text
File: test.txt

root → rw
bob  → rw
eve  → r
```

This is the main purpose of ACLs:

> **Give different users different permissions on the same file or directory without changing the owner or owning group.**