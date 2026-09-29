## Lesson: Groups in Linux

## 1. What is a Group?

A group in Linux is a collection of users who share common permissions to access files, directories, and system resources.

Instead of assigning permissions to every user individually, administrators can assign permissions to a group.

### Example

In a company, employees may belong to different teams:

* HR Team
* Finance Team
* IT Team
* Development Team

Each team requires access to specific resources.

Linux groups help administrators manage these permissions efficiently.

**Key idea:** A group is a way to manage permissions for multiple users together.

---

## 2. Primary vs Supplementary Groups

Every Linux user has a primary group and can also belong to one or more supplementary groups.

### Primary Group

* Every user has one primary group.
* Files created by the user normally inherit the user's primary group as their group owner.
* A user can have only one primary group at a time.

### Supplementary Groups

* A user can belong to multiple supplementary groups.
* Supplementary groups provide additional access to shared resources.
* They allow users to access files and directories associated with those groups.

### Example

```bash
id chandu
```

Example output:

```text
uid=1000(chandu) gid=1000(chandu) groups=1000(chandu),27(sudo)
```

Explanation:

| Field    | Meaning                                                           |
| -------- | ----------------------------------------------------------------- |
| uid=1000 | User ID                                                           |
| gid=1000 | Primary group ID                                                  |
| groups   | All groups the user belongs to                                    |
| sudo     | Supplementary group providing administrative privileges on Ubuntu |

---

## 3. How Linux Stores Group Information

Linux stores group information in the `/etc/group` file.

To view the file:

```bash
cat /etc/group
```

To inspect a particular group:

```bash
getent group developers
```

Example output:

```text
developers:x:1002:chandu
```

### Understanding the Format

```text
group_name:password:GID:members
```

| Field      | Description                                 |
| ---------- | ------------------------------------------- |
| group_name | Name of the group                           |
| password   | Usually `x` or an unused placeholder        |
| GID        | Group ID                                    |
| members    | Comma separated supplementary group members |

The group password field is generally not used for normal group administration.

Group definitions are commonly stored in `/etc/group`, while protected group password information, where applicable, is stored in `/etc/gshadow`.

---

## 4. View Existing Groups

### List all groups

```bash
cat /etc/group
```

### Search for a specific group

```bash
getent group developers
```

### View the current user's groups

```bash
groups
```

### View groups of a particular user

```bash
groups chandu
```

### View detailed user and group information

```bash
id chandu
```

**Practical use:** These commands help administrators verify group membership and troubleshoot permission issues.

---

## 5. Create a New Group

Use the `groupadd` command to create a new group.

### Syntax

```bash
sudo groupadd group_name
```

### Example

Create a development team group:

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

The group has been created and currently has no supplementary members.

---

## 6. Add a User to a Group

Use `usermod` to modify a user's group membership.

### Add a user to a supplementary group

```bash
sudo usermod -aG developers chandu
```

Explanation:

| Option       | Meaning                                                              |
| ------------ | -------------------------------------------------------------------- |
| `-a`         | Append the group without removing existing supplementary memberships |
| `-G`         | Specify supplementary groups                                         |
| `developers` | Group to add                                                         |
| `chandu`     | Username                                                             |

**Important:** Always use `-aG` when adding a supplementary group to an existing user. Using `-G` without `-a` can replace the user's existing supplementary group list.

### Verify membership

```bash
groups chandu
```

Or:

```bash
id chandu
```

---

## 7. Create a Group and Add Users

Example: Create a development group and add a user.

```bash
sudo groupadd developers
sudo usermod -aG developers chandu
getent group developers
```

To create a new user and assign a supplementary group:

```bash
sudo useradd -m -G developers newuser
```

Set the password:

```bash
sudo passwd newuser
```

Verify:

```bash
id newuser
```

Note that `useradd -G` assigns supplementary groups. It does not change the user's primary group unless `-g` is specified.

---

## 8. Remove a User from a Group

Use the `gpasswd` command to remove a user from a supplementary group.

### Syntax

```bash
sudo gpasswd -d username group_name
```

### Example

```bash
sudo gpasswd -d chandu developers
```

Verify:

```bash
groups chandu
```

The user will no longer have membership in the `developers` supplementary group.

Removing a user from a group does not delete the user account.

---

## 9. Delete a Group

Use `groupdel` to delete a group.

### Syntax

```bash
sudo groupdel group_name
```

### Example

```bash
sudo groupdel developers
```

Verify:

```bash
getent group developers
```

If the group no longer exists, the command returns no matching entry.

**Note:** A group cannot normally be deleted while it is the primary group of an existing user. Change the user's primary group first, if appropriate.

Deleting a group does not automatically delete files owned by that group's GID.

---

## 10. Understanding Group Membership Changes

When a user is added to a new group, the change may not immediately appear in the user's existing login session.

For example:

```bash
sudo usermod -aG developers chandu
```

The user may need to log out and log back in for the new supplementary group membership to apply to a new login session.

Alternatively, for a shell session, the user can start a new shell with a selected group:

```bash
newgrp developers
```

Verify the active groups:

```bash
id
```

Group membership changes and the groups active in a running process are not always the same thing. Existing processes retain their current group credentials.

---

## 11. Practical Lab: Development Team

### Scenario

You are a Linux system administrator in a company. A development team requires shared access to project resources.

Your task is to create a group, assign a user to it, verify membership, and remove the membership.

### Step 1: Create the group

```bash
sudo groupadd developers
```

### Step 2: Create a test user

```bash
sudo useradd -m chandu
sudo passwd chandu
```

If the user already exists, skip this step.

### Step 3: Add the user to the group

```bash
sudo usermod -aG developers chandu
```

### Step 4: Verify membership

```bash
id chandu
groups chandu
getent group developers
```

### Step 5: Remove the user

```bash
sudo gpasswd -d chandu developers
```

### Step 6: Verify removal

```bash
id chandu
```

### Step 7: Clean up the lab

If this is a disposable lab account, remove it after confirming that it is not being used:

```bash
sudo userdel -r chandu
```

Delete the group if it is no longer required:

```bash
sudo groupdel developers
```

---

## 12. Common Mistakes and Troubleshooting

| Mistake                                    | Explanation / Solution                                                                             |
| ------------------------------------------ | -------------------------------------------------------------------------------------------------- |
| Using `usermod -G` without `-a`            | Existing supplementary group memberships may be replaced. Use `-aG` to append.                     |
| New group does not appear immediately      | Start a new login session or use `newgrp` for a new shell.                                         |
| Permission denied despite group membership | Check file permissions, directory execute permissions, ACLs, and the groups active in the process. |
| Cannot delete a group                      | Check whether it is the primary group of an existing user.                                         |
| Group exists but has no members            | A group can exist without supplementary members. Check primary group assignments as well.          |

---

## 13. Security Best Practices

1. Follow the principle of least privilege. Give users only the group memberships they need.

2. Avoid adding ordinary users to privileged groups such as `sudo` unless administrative access is required.

3. Review group membership periodically.

4. Remove access when an employee changes teams or leaves the organization.

5. Use groups to manage shared file access instead of granting unnecessary permissions to everyone.

6. Verify group membership before troubleshooting access issues.

---

## 14. Quick Revision

| Command        | Purpose                             |
| -------------- | ----------------------------------- |
| `groups`       | View group memberships              |
| `id`           | View UID, GID, and groups           |
| `getent group` | Query group information             |
| `groupadd`     | Create a group                      |
| `usermod -aG`  | Add a user to supplementary groups  |
| `gpasswd -d`   | Remove a user from a group          |
| `groupdel`     | Delete a group                      |
| `newgrp`       | Start a shell with a selected group |

---

## Lesson Summary

Linux groups simplify user and permission management by allowing administrators to assign shared access to multiple users.

Understanding primary groups, supplementary groups, group membership changes, and permission verification is essential for Linux system administration and security monitoring.

**Next topic:** Linux File Permissions and Ownership (`chmod`, `chown`, `chgrp`, and `umask`).