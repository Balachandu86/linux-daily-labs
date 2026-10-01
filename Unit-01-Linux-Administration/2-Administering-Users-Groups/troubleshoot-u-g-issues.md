## Lesson: Troubleshoot User and Groups Issues

ISSUE
-----
Bob was unable to access a project file that should have
been accessible to members of the developers group.

APPROACH
--------
Investigate the user, group membership, group configuration,
file permissions, and every directory in the access path
before making any changes.

FINDINGS
--------
Bob exists.
Bob belongs to developers.
The developers group contains Bob.
The target file belongs to alice:developers.
The target file grants developers read/write access.
The project directory allows traversal.
The /home/alice directory belongs to alice:alice.
The /home/alice directory gives others no permissions.

DIAGNOSIS
---------
Bob cannot traverse /home/alice because he is not a member
of the alice group and the directory gives others no
permissions.

ROOT CAUSE
----------
Incorrect group/traversal configuration on /home/alice.

SOLUTION
--------
Change the group of /home/alice to developers:

sudo chgrp developers /home/alice

Set the directory permissions to:

sudo chmod 750 /home/alice

EXPECTED RESULT
---------------
/home/alice becomes:

drwxr-x--- alice developers

Alice retains full control.
Developers receive read/traverse access.
Others receive no access.

VERIFICATION
------------
Check the directory:

ls -ld /home/alice

Become Bob:

su - bob

Test reading:

cat /home/alice/project/project.txt

Test writing:

echo "Bob modified the project." >> /home/alice/project/project.txt

EXPECTED ACCESS
---------------
Read  → allowed
Write → allowed

VERIFICATION STATUS
-------------------
The actual post-fix command output was not recorded in the
provided troubleshooting session, so the solution was
identified and prepared for verification, but its successful
execution was not yet documented.







