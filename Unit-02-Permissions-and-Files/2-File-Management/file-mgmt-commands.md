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

