Editing files in Linux

Command	Purpose
cat	Display file contents and combine files
nano	Edit text files in the terminal
vim	Edit files using Vim
tee	Display output and write it to a file simultaneously

1. cat — Display file contents
cat file.txt


Other useful uses:
cat -n file.txt


Displays the file with line numbers.
cat file1.txt file2.txt


Displays both files sequentially.
cat file1.txt file2.txt > combined.txt


Combines their contents into combined.txt, overwriting it if it already exists.
Remember: cat displays or combines contents; it is not an interactive text editor.


2. nano — Edit files in the terminal
Concept: nano is a simple, beginner friendly text editor that lets you create and modify files directly from the terminal.
Practical
Create or open a file:
nano notes.txt


Type some text, for example:
Linux file editing practice
Learning Linux for cybersecurity


Essential keyboard shortcuts
Shortcut	Action
Ctrl + O	Save the file
Enter	Confirm the filename
Ctrl + X	Exit nano
Ctrl + W	Search text
Ctrl + K	Cut the current line
Ctrl + U	Paste the cut text
Remember: nano notes.txt opens an existing file or creates a new one if it doesn't exist.
Verify
After saving and exiting:
cat notes.txt


You should see the text you entered.
Cybersecurity use case: Editing configuration files, scripts, and lab notes from a Linux terminal.


