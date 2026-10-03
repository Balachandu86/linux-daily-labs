# Linux Privilege Escalation and Sudo Configuration

## 1. Privilege Escalation

Linux users normally operate with limited privileges.

Example:

```text
chandu
alice
bob
eve
   ↓
Limited privileges
```

The administrative account is:

```text
root
```

Root has extremely powerful privileges and can perform actions ordinary users cannot.

**Privilege escalation** means moving from a lower privilege level to a higher privilege level.

In this administration lab:

```text
Normal user
    ↓
sudo authorization
    ↓
Administrative command
```

The important security question is:

> Who is authorized to use administrative privileges, and what are they allowed to run?

---

## 2. `sudo`

`sudo` runs a command with another user's privileges, normally `root`.

Examples:

```bash
sudo ls /root
sudo systemctl status ssh
sudo whoami
```

A normal user remains logged in as that user. Only the specified command is executed with elevated privileges.

---

## 3. Check Sudo Access

Run:

```bash
sudo -l
```

This asks:

> What commands is this user allowed to run through sudo?

Example:

```text
User chandu may run the following commands:
    (ALL : ALL) ALL
```

This means Chandu has broad sudo privileges.

---

# 4. `su` vs `sudo`

## `su`

`su` switches to another user.

```bash
su - alice
```

You become Alice.

You can also attempt:

```bash
su -
```

to switch to root if the root account is configured for password authentication.

## `sudo`

`sudo` runs a particular command with elevated privileges:

```bash
sudo systemctl status ssh
```

You remain logged in as your normal user.

### Remember

```text
su
 ↓
Switch identity

sudo
 ↓
Run a command with elevated privileges
```

---

# 5. Understanding Chandu's Privileges

The user's identity was:

```text
uid=1000(chandu)
gid=1000(chandu)
```

Therefore:

```text
chandu
```

is a normal user, not root.

The user's groups included:

```text
groups=1000(chandu),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),100(users),114(lpadmin)
                                      ↑
                                    sudo
```

Chandu is a member of the `sudo` group.

On Ubuntu, membership in this group normally gives administrative access through `sudo`.

---

# 6. Demonstrating the Privilege Boundary

Run:

```bash
whoami
```

Result:

```text
chandu
```

Run:

```bash
id
```

Result:

```text
uid=1000(chandu) gid=1000(chandu) groups=...
```

Now run:

```bash
sudo whoami
```

Result:

```text
root
```

And:

```bash
sudo id
```

Result:

```text
uid=0(root) gid=0(root) groups=0(root)
```

### Key concept

```text
UID 1000
   ↓
chandu
   ↓
sudo
   ↓
UID 0
   ↓
root
```

UID `0` is the root account.

### Important

`sudo` does **not** permanently turn Chandu into root.

For example:

```bash
whoami
```

still returns:

```text
chandu
```

after running a previous `sudo` command.

Only the command executed through `sudo` receives elevated privileges.

---

# 7. How Sudo Is Controlled

Linux controls sudo access through sudoers configuration.

Main configuration:

```text
/etc/sudoers
```

Additional configuration:

```text
/etc/sudoers.d/
```

The main sudoers file can include the files in `/etc/sudoers.d/`.

## Important rule

Do not normally edit `/etc/sudoers` directly with `nano` or `vim`.

Use:

```bash
sudo visudo
```

`visudo` validates the sudoers syntax before installing the configuration, helping prevent a syntax mistake from breaking sudo access.

For a file inside `/etc/sudoers.d/`, use:

```bash
sudo visudo -f /etc/sudoers.d/adminlab
```

---

# 8. Validate Sudo Configuration

Run:

```bash
sudo visudo -c
```

Purpose:

> Check whether the sudoers configuration is syntactically valid.

Observed output:

```text
/etc/sudoers: parsed OK
/etc/sudoers.d/README: parsed OK
```

---

# 9. View Active Sudoers Lines

Run:

```bash
sudo grep -v '^#' /etc/sudoers | grep -v '^$'
```

This displays non-comment, non-empty lines from `/etc/sudoers`.

Observed configuration:

```text
Defaults    env_reset
Defaults    mail_badpass
Defaults    secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
Defaults    use_pty

root    ALL=(ALL:ALL) ALL
%admin  ALL=(ALL) ALL
%sudo   ALL=(ALL:ALL) ALL

@includedir /etc/sudoers.d
```

## Important rule

```text
%sudo ALL=(ALL:ALL) ALL
```

means members of the `sudo` group can broadly run commands through sudo as any user and group, subject to the sudo configuration.

Because Chandu belongs to the `sudo` group, Chandu matches this rule.

Therefore:

```bash
sudo whoami
```

returns:

```text
root
```

### Security lesson

Privilege escalation is not simply:

```text
"Run sudo"
```

The real security question is:

> Who is authorized to use sudo, and what are they authorized to run?

---

# 10. Lab: Create a Restricted Test User

Instead of changing Chandu's existing unrestricted sudo access, create a dedicated test user.

## Step 1: Create the user

```bash
sudo adduser adminlab
```

Verify:

```bash
id adminlab
groups adminlab
```

`adminlab` should **not** be in the `sudo` group.

Do not add `adminlab` to the sudo group.

---

# 11. Lab: Confirm the User Has No Sudo Access

Switch to the test user:

```bash
su - adminlab
```

Try:

```bash
sudo whoami
```

It should fail because `adminlab` has not been authorized to use sudo.

Return to Chandu:

```bash
exit
```

---

# 12. Lab: Create a Restricted Sudo Rule

As Chandu, create a dedicated sudoers configuration:

```bash
sudo visudo -f /etc/sudoers.d/adminlab
```

Add exactly:

```text
adminlab ALL=(root) /usr/bin/id
```

Save and exit.

### If using nano

```text
Ctrl + O
Enter
Ctrl + X
```

### If using vim

```text
Esc
:wq
Enter
```

---

# 13. Understand the Restricted Rule

```text
adminlab ALL=(root) /usr/bin/id
```

Conceptually, this says:

> `adminlab` is allowed to execute `/usr/bin/id` as root.

It does **not** give `adminlab` unrestricted root access.

This demonstrates the principle of:

## Least Privilege

Give a user only the administrative permission they actually need.

---

# 14. Lab: Validate the New Configuration

Run:

```bash
sudo visudo -c
```

Expected output includes:

```text
/etc/sudoers: parsed OK
/etc/sudoers.d/adminlab: parsed OK
```

This confirms that the new sudoers rule has valid syntax.

---

# 15. Lab: Check What `adminlab` Can Run

Run:

```bash
sudo -l -U adminlab
```

The output should show that `adminlab` is allowed to run:

```text
/usr/bin/id
```

as root.

---

# 16. Lab: Test the Restricted Privilege

Switch to `adminlab`:

```bash
su - adminlab
```

Run:

```bash
sudo id
```

This should work and show:

```text
uid=0(root)
```

Now try:

```bash
sudo whoami
```

This should be denied because `/usr/bin/whoami` was not authorized.

Return to Chandu:

```bash
exit
```

---

# 17. Practical Result

The lab demonstrated:

```text
adminlab
   │
   ├── sudo id       ✅ allowed
   │
   └── sudo whoami   ❌ denied
```

Compared with Chandu:

```text
chandu
   │
   └── sudo *
          ↓
       allowed
```

Therefore:

```text
Broad sudo access
        vs
Restricted sudo access
```

The restricted configuration follows the **principle of least privilege**.

---

# 18. Why the Exact Command Path Matters

The sudo rule used:

```text
/usr/bin/id
```

rather than simply:

```text
id
```

A sudo rule can be command specific, and the exact command path matters.

The practical rule was:

```text
adminlab ALL=(root) /usr/bin/id
```

Therefore the authorization is tied to that specific executable.

---

# 19. Complete Practical Command List

### Check current sudo permissions

```bash
sudo -l
```

### Check current identity

```bash
whoami
id
```

### Demonstrate elevated identity

```bash
sudo whoami
sudo id
```

### Validate sudo configuration

```bash
sudo visudo -c
```

### Display active lines from main sudoers file

```bash
sudo grep -v '^#' /etc/sudoers | grep -v '^$'
```

### Create test user

```bash
sudo adduser adminlab
```

### Check user's identity and groups

```bash
id adminlab
groups adminlab
```

### Switch users

```bash
su - adminlab
```

### Test sudo

```bash
sudo whoami
```

### Return to previous user

```bash
exit
```

### Create dedicated sudoers rule

```bash
sudo visudo -f /etc/sudoers.d/adminlab
```

Rule:

```text
adminlab ALL=(root) /usr/bin/id
```

### Validate again

```bash
sudo visudo -c
```

### Check a user's sudo permissions

```bash
sudo -l -U adminlab
```

### Test restricted privilege

```bash
sudo id
sudo whoami
```

---

# 20. Key Takeaways

1. **Root** has UID `0` and full administrative privileges.

2. A normal user such as Chandu can temporarily execute commands with root privileges through `sudo` when authorized.

3. `sudo -l` shows what a user is allowed to run through sudo.

4. `su` switches identity, while `sudo` normally elevates an individual command.

5. Chandu's membership in the `sudo` group explains the broad sudo access on this Ubuntu system.

6. `/etc/sudoers` is the main sudo configuration file.

7. `/etc/sudoers.d/` provides additional sudo configuration files.

8. `visudo` should be used to edit sudoers because it validates syntax before applying the configuration.

9. `sudo visudo -c` checks sudoers syntax without modifying the configuration.

10. A rule such as:

```text
adminlab ALL=(root) /usr/bin/id
```

provides restricted administrative access rather than unrestricted root access.

11. The practical difference was:

```text
sudo id       → allowed
sudo whoami   → denied
```

12. This demonstrates the **principle of least privilege**.

13. The exact executable path in a sudo rule matters.

---

# Lab Flow

```text
Normal User
    ↓
Check identity
    ↓
Check sudo permissions
    ↓
Understand /etc/sudoers
    ↓
Create adminlab
    ↓
Confirm no sudo access
    ↓
Create restricted sudo rule
    ↓
Validate with visudo
    ↓
Check permissions with sudo -l -U
    ↓
Test allowed command
    ↓
Test denied command
    ↓
Understand least privilege
```

**Core concept:**

> Secure privilege management is not about giving users root access. It is about giving the right users the minimum administrative access required for their tasks.