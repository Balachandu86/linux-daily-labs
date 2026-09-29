# Lesson: Bash Interaction in Linux

## 1. Bash Shell

Bash stands for Bourne Again Shell.

It allows users to interact with Linux through commands.

## 2. Command Structure

```bash
command [options] [arguments]
```

Example:

```bash
ls -l /home
```

`ls` = command
`-l` = option
`/home` = argument

## 3. Tab Completion

Press `Tab` to complete commands, filenames, and directories.

Useful for saving time and avoiding typing mistakes.

If multiple matches exist, press `Tab` twice to display them.

## 4. Command History

| Command   | Use                                   |
| --------- | ------------------------------------- |
| `history` | Displays previously executed commands |
| `↑`       | Accesses previous commands            |
| `↓`       | Moves forward through command history |

## 5. Getting Help

| Command     | Use                                      |
| ----------- | ---------------------------------------- |
| `man ls`    | Opens the manual for `ls`                |
| `ls --help` | Displays command help                    |
| `help`      | Displays help for Bash built in commands |

### Inside the Manual

| Key     | Use           |
| ------- | ------------- |
| `Space` | Next page     |
| `b`     | Previous page |
| `q`     | Quit          |

## 6. Basic Navigation Commands

| Command    | Use                                |
| ---------- | ---------------------------------- |
| `pwd`      | Displays current directory         |
| `ls`       | Lists files and directories        |
| `ls -l`    | Displays detailed file information |
| `ls -a`    | Shows hidden files and directories |
| `cd /home` | Changes directory to `/home`       |
| `cd ..`    | Moves to parent directory          |
| `cd ~`     | Moves to home directory            |

## Practice

```bash
pwd
ls
ls -l
ls -a
cd ..
cd ~
history
man ls
```

**Objective:** Learn basic command structure, terminal navigation, command history, and how to get help in Linux.