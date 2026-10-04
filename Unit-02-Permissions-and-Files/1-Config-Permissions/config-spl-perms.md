1. What is SUID?

SUID = Set User ID

Normally, when you execute a program:

Your user
   ↓
Program
   ↓
Runs with your privileges

With SUID:

Your user
   ↓
SUID program
   ↓
Runs with the file owner's effective privileges

So if a program is:

Owner = root
SUID  = enabled

a normal user executing that program can have the program run with root's effective UID.

This is why SUID is important in cybersecurity.

For example, traditionally:

/usr/bin/passwd
Owner: root
SUID: enabled

A normal user can run:

passwd

and modify their password, even though the underlying password database is protected from ordinary users.

Security perspective

The important point is:

The user does not become root. The SUID program temporarily executes with the owner's effective privileges.



2. How do we recognize SUID?

Normally:

-rwxr-xr-x

With SUID:

-rwsr-xr-x

Notice:

rwx
 ↓
rws

The x position for the owner becomes s.

You can also express SUID numerically.

4 = SUID
2 = SGID
1 = Sticky Bit

So:

chmod 4755 file

means:

SUID + 755


3. A real Linux example
Ubuntu has programs that legitimately use SUID.
Let's inspect one:
ls -l /usr/bin/passwd

You will likely see something similar to:
-rwsr-xr-x 1 root root ... /usr/bin/passwd

The important part is:
root
 ↓
owner

s
 ↓
SUID

Why would passwd need special privileges?
A normal user needs to be able to change their password, but password information is protected by privileged system files. The SUID mechanism allows the program to perform the privileged operation while the user remains a normal user.
4. Very important distinction
SUID does not mean:
"The user becomes root."

It means:
The SUID executable runs with the file owner's effective privileges.

For example:
Owner = root
SUID  = enabled

can result in:
User: bob

       ↓

SUID program

       ↓

Effective UID: root

But Bob's normal shell is still Bob.


🧪 Practical Lab: See SUID in Action
We're going to create a safe SUID demonstration using /usr/bin/id.
We will make a copy owned by alice, not root, so we're not creating a root privilege escalation path.
Step 1
As chandu, run:
cp /usr/bin/id /tmp/id-alice

Then:
sudo chown alice:alice /tmp/id-alice

Check:
ls -l /tmp/id-alice

You should see something like:
-rwxr-xr-x 1 alice alice ... /tmp/id-alice

Step 2: Enable SUID
Run:
sudo chmod u+s /tmp/id-alice

Check:
ls -l /tmp/id-alice

Now you should see:
-rwsr-xr-x 1 alice alice ... /tmp/id-alice

The important change is:
x
↓
s

Step 3: Test as Bob
Switch to Bob:
su - bob

Run the normal command:
id

Then run:
/tmp/id-alice

The two outputs should show the difference between Bob's normal identity and the SUID program's effective identity.
Then return:
exit

🧠 What this lab demonstrates
Without SUID:
Bob
 ↓
id
 ↓
Bob's privileges

With SUID:
Bob
 ↓
/tmp/id-alice
 ↓
Alice's effective privileges

So the owner of the executable matters.
That's the core idea of SUID.
Security relevance
An administrator can search for SUID executables with:
find / -perm -4000 -type f 2>/dev/null

This finds regular files with the SUID bit set.
In security assessments, unusual or unnecessarily privileged SUID programs can deserve investigation because a vulnerable or improperly configured SUID program may allow privilege escalation.





SUID owned by root
Remember our previous example:
-rwsr-xr-x 1 alice alice ...

Chandu executed it and got:
uid=1000(chandu) euid=1001(alice)

Now imagine the same thing is owned by root:
-rwsr-xr-x 1 root root ...

Then:
Normal user
    ↓
Root owned SUID program
    ↓
Effective UID = 0
    ↓
root privileges

The user does not become root as a shell user. The SUID program executes with root's effective privileges.
Real Linux example
Check:
ls -l /usr/bin/passwd

On a typical Ubuntu installation, you'll see an s in the owner's execute position, something like:
-rwsr-xr-x 1 root root ... /usr/bin/passwd

That SUID bit is intentional. It allows ordinary users to perform password related operations that require access beyond their normal permissions.

🧪 Let's demonstrate it safely
We'll use the same harmless id program we used before.
As chandu:
sudo cp /usr/bin/id /tmp/id-root
sudo chown root:root /tmp/id-root
sudo chmod 4755 /tmp/id-root

Check:
ls -l /tmp/id-root

You should get:
-rwsr-xr-x 1 root root ... /tmp/id-root

Now run:
/tmp/id-root

You should see the important difference:
uid=1000(chandu) ... euid=0(root)

So:
uid  → 1000 → chandu
euid → 0    → root

That is root owned SUID.
Why is this dangerous?
Because the program is running with root's effective privileges.
If that program contains a security vulnerability, an attacker may be able to make the program perform unintended actions with those root privileges.
That's why security administrators often audit SUID files:
find / -perm -4000 -type f 2>/dev/null

The important security question isn't:
"Is SUID present?"

It is:
"Is this SUID program necessary, trusted, and securely configured?"

Clean up our lab
After testing:
sudo rm /tmp/id-root
sudo rm /tmp/id-alice


