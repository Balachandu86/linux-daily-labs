## Lesson: Linux File Permissions and Group Management

**Category:** Linux System Administration  
**Topics:** File Permissions, Ownership, User and Group Management

---

# 1. Linux File Permissions

Linux file permissions determine who can access, modify, or execute files and directories.

Unlike Windows, Linux uses a permission model based on three categories:

| Category | Description |
|---|---|
| User (u) | Owner of the file |
| Group (g) | Users belonging to the file's group |
| Others (o) | All other users |

## 1.1 Permission Types

| Permission | Symbol | Numeric Value | Description |
|---|---|---|---|
| Read | r | 4 | View file contents or list directory contents |
| Write | w | 2 | Modify a file or create and delete directory entries |
| Execute | x | 1 | Execute a file or access a directory |

### Example

```bash
ls -l
```

Output:

```text
-rw-r--r-- 1 chandu chandu 4096 Sep 29 notes.txt
```

Understanding the permissions:

```text
- rw- r-- r--
  |   |   |
  |   |   +--- Others
  |   +------- Group
  +----------- Owner
```

The owner can read and write, while the group and others can only read.

---

# 2. Practical Lab: Create a Permission Lab

## Step 1: Create the Lab Directory

```bash
mkdir permission_lab
cd permission_lab
```

Create a sample file:

```bash
touch notes.txt
```

Add content:

```bash
echo "Hello Linux" > notes.txt
```

Check the file permissions:

```bash
ls -l
```

## Step 2: Change File Permissions Using chmod

The `chmod` command changes file permissions.

Syntax:

```bash
chmod permissions filename
```

### Method 1: Symbolic Permissions

Give the owner read and write permissions:

```bash
chmod u+rw notes.txt
```

Remove write permission from others:

```bash
chmod o-w notes.txt
```

Give the group read permission:

```bash
chmod g+r notes.txt
```

### Method 2: Numeric Permissions

Each permission has a numeric value:

```text
Read    = 4
Write   = 2
Execute = 1
```

Examples:

| Command | Permission | Meaning |
|---|---|---|
| `chmod 777 notes.txt` | rwx rwx rwx | Everyone has full permissions |
| `chmod 755 notes.txt` | rwx r-x r-x | Owner has full access; others can read and execute |
| `chmod 644 notes.txt` | rw- r-- r-- | Owner can read and write; others can read |
| `chmod 600 notes.txt` | rw- --- --- | Only owner can read and write |

For a regular text file:

```bash
chmod 644 notes.txt
```

Verify:

```bash
ls -l notes.txt
```

**Security Note:** Avoid using `chmod 777` on sensitive files because it grants write and execute permissions to everyone.

---

# 3. File Ownership: chown and chgrp

Linux files have an owner and an associated group.

Ownership answers the question:

**Who owns this file?**

## 3.1 Check Ownership

```bash
ls -l notes.txt
```

Example:

```text
-rw-r--r-- 1 chandu developers 4096 Sep 29 notes.txt
```

Here:

- Owner: `chandu`
- Group: `developers`

Check the current user:

```bash
whoami
```

Check user ID and group memberships:

```bash
id
```

Display all groups associated with the current user:

```bash
groups
```

## 3.2 Change File Owner Using chown

The `chown` command changes file ownership.

Syntax:

```bash
sudo chown username filename
```

Example:

```bash
sudo chown newuser notes.txt
```

This changes the owner to `newuser`.

## 3.3 Change File Group Using chgrp

The `chgrp` command changes the group associated with a file.

```bash
sudo chgrp developers notes.txt
```

Verify:

```bash
ls -l notes.txt
```

## 3.4 Change Owner and Group Together

```bash
sudo chown chandu:developers notes.txt
```

This sets:

- Owner: `chandu`
- Group: `developers`

---

# 4. Linux Group Management

## 4.1 What Is a Group?

A Linux group is a collection of user accounts.

Groups make it easier to assign permissions to multiple users instead of managing permissions for every user individually.

Examples of organizational groups:

- HR Team
- Finance Team
- IT Team
- Developers

A company can use Linux groups to control access to shared files, directories, and applications.

## 4.2 Primary and Supplementary Groups

Every Linux user has a primary group and may belong to additional supplementary groups.

| Type | Description |
|---|---|
| Primary group | Default group associated with the user's files and processes |
| Supplementary group | Additional groups that provide access to shared resources |

Example:

```bash
id chandu
```

Sample output:

```text
uid=1000(chandu) gid=1000(chandu) groups=1000(chandu),1001(developers)
```

Here:

- `chandu` is the primary group.
- `developers` is a supplementary group.

---

# 5. Practical Lab: Create and Manage Groups

## Step 1: Create a New Group

Create a group called `developers`:

```bash
sudo groupadd developers
```

Verify the group:

```bash
getent group developers
```

Example output:

```text
developers:x:1002:
```

## Step 2: Create a New User

Create a user named `devuser`:

```bash
sudo adduser devuser
```

Follow the prompts to set a password and user details.

Alternatively, create a user with a home directory:

```bash
sudo useradd -m devuser
```

Set the password:

```bash
sudo passwd devuser
```

## Step 3: Add a User to a Group

Add `devuser` to the supplementary `developers` group:

```bash
sudo usermod -aG developers devuser
```

Important:

The `-aG` options append the supplementary group without removing existing supplementary memberships.

Avoid using `usermod -G` without `-a` unless you intend to replace the user's supplementary group list.

Verify:

```bash
id devuser
```

## Step 4: Verify Group Membership

```bash
groups devuser
```

Expected output includes:

```text
devuser developers
```

For the current user:

```bash
groups
```

**Note:** Existing login sessions may not immediately reflect new supplementary group memberships. Log out and log back in, or start a new session, to apply the updated membership.

## Step 5: Remove a User from a Group

On Ubuntu, use:

```bash
sudo deluser devuser developers
```

Verify:

```bash
groups devuser
```

## Step 6: Delete a Group

```bash
sudo groupdel developers
```

A group cannot normally be deleted if it is the primary group of an existing user.

---

# 6. Group-Based File Access Lab

This exercise demonstrates how multiple users can access shared files through a common group.

## Step 1: Create a Shared Directory

```bash
sudo mkdir /opt/dev-project
```

## Step 2: Assign Group Ownership

```bash
sudo chown :developers /opt/dev-project
```

## Step 3: Set Directory Permissions

```bash
sudo chmod 770 /opt/dev-project
```

Permissions:

```text
Owner:   rwx
Group:   rwx
Others:  ---
```

Only the owner and members of the developers group can access the directory.

## Step 4: Verify Permissions

```bash
ls -ld /opt/dev-project
```

Expected format:

```text
drwxrwx--- root developers ... /opt/dev-project
```

## Step 5: Test Access

Switch to the test user:

```bash
su - devuser
```

Try creating a file:

```bash
touch /opt/dev-project/test.txt
```

If `devuser` belongs to the developers group and has an active session with that membership, the operation should succeed.

Exit the user session:

```bash
exit
```

---

# 7. Real-World Scenario

## Scenario: Development Team Shared Directory

A company has three developers who need to work on the same project directory.

Instead of granting access individually, the administrator creates a shared group.

```bash
sudo groupadd developers

sudo usermod -aG developers devuser

sudo mkdir -p /opt/dev-project

sudo chown root:developers /opt/dev-project

sudo chmod 2770 /opt/dev-project
```

The leading `2` sets the setgid bit on the directory.

New files and subdirectories created inside the directory normally inherit the directory's group, making collaboration easier.

The directory permissions are:

```text
drwxrws---
```

The `s` in the group execute position indicates the setgid bit.

---

# 8. Troubleshooting and Security Notes

| Problem | Possible Solution |
|---|---|
| Permission denied | Check file ownership, permissions, and directory access |
| User cannot access a shared directory | Verify group membership and start a new login session |
| Cannot change ownership | Use `sudo` or an appropriately privileged account |
| Group deletion fails | Check whether the group is a user's primary group |
| User loses existing supplementary groups | Use `usermod -aG` instead of replacing the group list |

### Security Best Practices

- Follow the principle of least privilege.
- Avoid granting unnecessary write access.
- Avoid using `chmod 777` on sensitive files.
- Use groups to manage shared access.
- Verify ownership and permissions regularly.
- Use `sudo` only when administrative privileges are required.

---

# 9. Commands Reference

| Command | Purpose |
|---|---|
| `ls -l` | View file permissions and ownership |
| `chmod` | Change file permissions |
| `chown` | Change file owner or group |
| `chgrp` | Change file group |
| `whoami` | Display current username |
| `id` | Display user and group IDs |
| `groups` | Display group memberships |
| `groupadd` | Create a group |
| `groupdel` | Delete a group |
| `adduser` | Create a user on Ubuntu |
| `usermod -aG` | Add a user to a supplementary group |
| `getent group` | Display group database entries |
| `deluser` | Remove a user or group membership on Ubuntu |

---

# 10. Learning Outcomes

After completing this lab, I can:

- Explain Linux file permission types and numeric values.
- Read and interpret the output of `ls -l`.
- Modify permissions using `chmod`.
- Change ownership using `chown` and `chgrp`.
- Create and manage Linux users and groups.
- Configure shared directories for team collaboration.
- Apply least privilege to Linux file access.

**Lab Status:** Completed

**Next Lesson:** Linux Process Management