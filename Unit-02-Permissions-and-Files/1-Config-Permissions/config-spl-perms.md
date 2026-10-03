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