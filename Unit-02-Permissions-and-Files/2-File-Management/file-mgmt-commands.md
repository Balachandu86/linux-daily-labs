1. mkdir — Create directories
Concept
mkdir creates a directory.
mkdir dirname

Example:
mkdir /tmp/file-lab

Verify:
ls -ld /tmp/file-lab

Useful option
mkdir -p /tmp/file-lab/a/b/c

-p creates the parent directories if they don't already exist.
2. touch — Create an empty file
touch /tmp/file-lab/test.txt

Verify:
ls -l /tmp/file-lab/test.txt

Important: touch can also update a file's timestamps if the file already exists.
3. cp — Copy
Copy a file:
cp /tmp/file-lab/test.txt /tmp/file-lab/test-copy.txt

Copy a directory:
cp -r /tmp/file-lab /tmp/file-lab-backup

-r means recursive, so directories and their contents are copied.
4. mv — Move or rename
Rename:
mv /tmp/file-lab/test.txt /tmp/file-lab/renamed.txt

Move:
mv /tmp/file-lab/renamed.txt /tmp/

Same command, two common uses:
mv → rename
mv → move

5. rm — Remove
Remove a file:
rm /tmp/test.txt

Remove a directory and its contents:
rm -r /tmp/file-lab

Force removal:
rm -f filename

Recursive + force:
rm -rf directory

⚠️ rm -rf is powerful because Linux normally does not provide a recycle bin for this command.
Practical Lab
Let's do everything in one controlled directory:
mkdir /tmp/file-lab
cd /tmp/file-lab

Then:
touch file1.txt
mkdir dir1
cp file1.txt file2.txt
mv file2.txt dir1/
ls -l
ls -l dir1/

Finally:
rm dir1/file2.txt
rmdir dir1

Notice that we're using rmdir here because dir1 should now be empty.
Core commands to remember
mkdir → create directory
touch → create file
cp    → copy
mv    → move / rename
rm    → remove
rmdir → remove empty directory


1. tree
Shows directories and files in a tree structure.
tree /tmp/file-lab

If not installed:
sudo apt install tree

Useful:
tree -L 2

-L 2 limits the display to two levels.
2. pwd
Shows your current working directory.
pwd

Example:
/home/chandu

Remember:
pwd = Print Working Directory

3. less and more
Used to read large files without dumping everything onto the terminal.
less /var/log/syslog

Useful less navigation:
Space → next page
b     → previous page
↑/↓   → move
/word → search
q     → quit

more:
more /var/log/syslog

Difference
less → more capable, commonly preferred
more → simpler pager

4. head
Shows the beginning of a file.
head file.txt

Default is usually 10 lines.
-n
head -n 5 file.txt

Shows the first 5 lines.
You can also use:
head -n 20 file.txt

5. tail
Shows the end of a file.
tail file.txt

Default is usually 10 lines.
-n
tail -n 5 file.txt

Shows the last 5 lines.
Very important for cybersecurity/SOC work:
tail -f /var/log/syslog

-f follows the file and displays new lines as they are added.
6. grep
One of the most important commands for Linux administration and SOC work.
It searches text for a pattern.
Example:
grep "error" logfile.txt

Important options
grep -i "error" logfile.txt

-i → case insensitive
grep -n "error" logfile.txt

-n → show line numbers
grep -v "error" logfile.txt

-v → show lines that do not match
grep -r "password" /etc/

-r → recursively search directories
grep -w "root" file.txt

-w → match the whole word
grep -c "error" logfile.txt

-c → count matching lines
Combine options:
grep -in "error" logfile.txt

Very common combination
grep -i "failed" /var/log/auth.log

This is directly useful when investigating authentication failures.
7. Redirection
Linux commands have three standard streams:
stdin   → 0 → input
stdout  → 1 → normal output
stderr  → 2 → error output

Think:
stdin  → command
stdout ← command
stderr ← command

> stdout redirection
ls > output.txt

Instead of displaying the output, it goes into output.txt.
Overwrites the file.
>>
ls >> output.txt

Appends to the file.
< stdin
sort < names.txt

The contents of names.txt become the command's input.
2> stderr
ls /does-not-exist 2> error.txt

Errors go into error.txt.
Combine stdout and stderr
command > output.txt 2> error.txt

Or:
command > all.txt 2>&1

Meaning:
stdout → all.txt
stderr → same place

8. Command chaining
;
Run the next command regardless of whether the previous one succeeds.
mkdir test; echo "Done"

&&
Run the second command only if the first succeeds.
mkdir test && echo "Created"

Very commonly used.
||
Run the second command only if the first fails.
mkdir test || echo "Failed"

| Pipe
Pass stdout of one command as stdin to another.
ls /etc | grep ssh

Think:
ls /etc
   ↓
 grep ssh

This is extremely important in Linux.
Example:
ps aux | grep nginx

!
Negates the exit status of a command.
! grep "root" file.txt

Conceptually:
command succeeds → ! makes result fail
command fails     → ! makes result succeed

9. xargs
xargs takes input from stdin and turns it into arguments for another command.
Simple example:
echo "file1.txt file2.txt" | xargs ls -l

Conceptually:
echo
 ↓
file1.txt file2.txt
 ↓
xargs
 ↓
ls -l file1.txt file2.txt

Practical example
find /tmp/file-lab -name "*.txt" | xargs ls -l

Finds .txt files and passes them to ls.
Another useful example:
printf "one\ntwo\nthree\n" | xargs -n 1 echo

-n 1 means one input item per command invocation.
Important caution
With filenames containing spaces or special characters, plain xargs can behave incorrectly. A safer pattern with find is:
find . -name "*.txt" -print0 | xargs -0 ls -l

You don't need to memorize the advanced form yet. Just understand why -0 exists.
What you should remember for interviews
tree       → directory structure
pwd        → current directory
less/more  → read files page by page
head       → beginning of file
tail       → end of file
grep       → search text
>          → overwrite stdout
>>         → append stdout
<          → stdin
2>         → stderr
|          → pipe output to another command
;          → run regardless
&&         → run if success
||         → run if failure
!          → negate exit status
xargs      → stdin → command arguments