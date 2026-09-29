# Lesson: Linux User Management

## 1. User Information

| Command       | Use                                        |
| ------------- | ------------------------------------------ |
| `whoami`      | Displays current username                  |
| `id`          | Displays user ID (UID) and group ID (GID)  |
| `id username` | Displays information about a specific user |
| `groups`      | Displays current user's groups             |
| `who`         | Shows logged in users                      |

## 2. User Files

| File          | Use                                                   |
| ------------- | ----------------------------------------------------- |
| `/etc/passwd` | Stores user account information                       |
| `/etc/shadow` | Stores password hashes and password aging information |
| `/etc/group`  | Stores group information                              |

```bash
cat /etc/passwd
sudo cat /etc/shadow
cat /etc/group
```

Note: `/etc/shadow` is restricted because it contains sensitive password information.

## 3. Creating Users

| Command                    | Use                                  |
| -------------------------- | ------------------------------------ |
| `sudo adduser username`    | Creates a user interactively         |
| `sudo useradd username`    | Creates a user account               |
| `sudo useradd -m username` | Creates a user with a home directory |
| `sudo passwd username`     | Sets or changes user password        |

Example:

```bash
sudo adduser chandu
```

## 4. Modifying Users

| Command                           | Use                     |
| --------------------------------- | ----------------------- |
| `sudo usermod -aG sudo username`  | Adds user to sudo group |
| `sudo usermod -l newname oldname` | Changes username        |
| `sudo passwd -l username`         | Locks user password     |
| `sudo passwd -u username`         | Unlocks user password   |

## 5. Deleting Users

| Command                    | Use                             |
| -------------------------- | ------------------------------- |
| `sudo userdel username`    | Deletes user account            |
| `sudo userdel -r username` | Deletes user and home directory |

## 6. Group Management

| Command                                | Use                    |
| -------------------------------------- | ---------------------- |
| `sudo groupadd developers`             | Creates a group        |
| `sudo usermod -aG developers username` | Adds user to a group   |
| `groups username`                      | Displays user's groups |
| `sudo groupdel developers`             | Deletes a group        |

## Practice

```bash
whoami
id
groups
cat /etc/passwd

sudo adduser testuser
sudo passwd testuser
sudo usermod -aG sudo testuser
id testuser
sudo userdel -r testuser
```

**Objective:** Learn to create, modify, inspect, and delete Linux users and groups.
