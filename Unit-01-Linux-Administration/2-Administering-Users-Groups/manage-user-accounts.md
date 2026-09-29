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
## Lab Practice on users

chandu@chandu-VirtualBox:~$ sudo adduser testuser
info: Adding user `testuser' ...
info: Selecting UID/GID from range 1000 to 59999 ...
info: Adding new group `testuser' (1006) ...
info: Adding new user `testuser' (1006) with group `testuser (1006)' ...
info: Creating home directory `/home/testuser' ...
info: Copying files from `/etc/skel' ...
New password: 
BAD PASSWORD: The password is shorter than 8 characters
Retype new password: 
passwd: password updated successfully
Changing the user information for testuser
Enter the new value, or press ENTER for the default
	Full Name []: 
	Room Number []: 
	Work Phone []: 
	Home Phone []: 
	Other []: 
Is the information correct? [Y/n] Y
info: Adding new user `testuser' to supplemental / extra groups `users' ...
info: Adding user `testuser' to group `users' ...
chandu@chandu-VirtualBox:~$ id testuer
id: ‘testuer’: no such user
chandu@chandu-VirtualBox:~$ id testuser
uid=1006(testuser) gid=1006(testuser) groups=1006(testuser),100(users)
chandu@chandu-VirtualBox:~$ ls /home
alice  bob  chandu  eve  testuser
chandu@chandu-VirtualBox:~$ sudo userdel -r testuser
userdel: testuser mail spool (/var/mail/testuser) not found
chandu@chandu-VirtualBox:~$ id testuser
id: ‘testuser’: no such user

## Step 1 - Create a test user

sudo adduser testuser and set passwd "Test123"

## Step 2 - Verify the user exists

id testuser

## Step 3 - Check the new home dir

ls /home

## Step 4 - Delete the testuser and its home dir

sudo userdel -r testuser

## Step 5 - Verify it's gone

id testuser

**Objective:** Learn to create, modify, inspect, and delete Linux users and groups.