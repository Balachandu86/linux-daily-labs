# Lesson: Introduction to Linux

## 1. What is Linux?

Linux is an open source operating system kernel created by Linus Torvalds in 1991.

The kernel is the core component of an operating system. It acts as a bridge between applications and computer hardware.

### Responsibilities of the Linux Kernel

The kernel manages:

1. Memory management
2. CPU and process management
3. File system management
4. Hardware and device control

**In simple words:** The kernel acts as the manager of the computer, coordinating applications and hardware resources.

### How Applications Interact with Hardware

Applications do not normally communicate directly with hardware. They request services from the kernel, which manages access to hardware.

```text
Applications
     |
     v
Linux Kernel
     |
     v
Computer Hardware
```

The kernel provides a controlled way for applications to access system resources.

---

## 2. Why is Linux Popular?

Linux is widely used because of its flexibility, stability, security features, and open source nature.

| Feature             | Description                                                     |
| ------------------- | --------------------------------------------------------------- |
| Open Source         | Its source code is publicly available.                          |
| Highly Customizable | Can be modified according to specific requirements.             |
| Security            | Provides permissions, ownership, and access control mechanisms. |
| Stability           | Commonly used for systems that need to run continuously.        |
| Cost Effective      | Many Linux distributions are available free of charge.          |

Linux is widely used in servers, cloud platforms, and cybersecurity environments.

---

## 3. Where is Linux Used?

Linux powers many technologies used in everyday life and enterprise environments.

| Area            | Examples                                  |
| --------------- | ----------------------------------------- |
| Web Servers     | Hosting websites and web applications     |
| Cloud Platforms | Virtual machines and cloud infrastructure |
| Supercomputers  | High performance computing                |
| Mobile Devices  | Android uses the Linux kernel.            |
| IoT Devices     | Embedded systems and smart devices        |
| Cybersecurity   | Kali Linux and security testing tools     |

### Linux in Cybersecurity

Linux is particularly important in cybersecurity because many security tools, servers, and infrastructure systems run on Linux.

Examples include Kali Linux, penetration testing tools, security monitoring systems, and server security environments.

---

## 4. Linux Distributions

A Linux distribution, or distro, is an operating system built around the Linux kernel with additional software, system utilities, and package management tools.

Different distributions are designed for different purposes.

| Distribution                    | Description                                                     |
| ------------------------------- | --------------------------------------------------------------- |
| Ubuntu                          | General purpose, desktop, server, and cloud use                 |
| Debian                          | Stable distribution and the foundation of Ubuntu and Kali Linux |
| Kali Linux                      | Designed for penetration testing and security auditing          |
| Fedora                          | Community distribution sponsored by Red Hat                     |
| Red Hat Enterprise Linux (RHEL) | Enterprise Linux distribution                                   |

### Important Note

Ubuntu and Kali Linux are based on Debian. Fedora and RHEL belong to the Red Hat ecosystem.

---

## 5. Basic Linux Commands

These commands help identify the operating system, system information, hostname, and current working directory.

### 5.1 uname

Displays basic information about the system kernel.

```bash
uname
```

Output example:

```text
Linux
```

### 5.2 uname -a

Displays all available basic system information, including kernel name, hostname, kernel release, kernel version, machine architecture, and other details.

```bash
uname -a
```

Useful for checking the kernel version and system architecture.

### 5.3 hostname

Displays the name assigned to the computer.

```bash
hostname
```

A hostname identifies a system on a network.

Examples include:

```text
web-server
database-server
linux-vm
```

### 5.4 hostnamectl

Displays detailed information about the system hostname and operating system.

```bash
hostnamectl
```

It may display:

* Static hostname
* Operating system
* Kernel version
* Hardware architecture

### 5.5 cat /etc/os-release

Displays information about the installed Linux distribution.

```bash
cat /etc/os-release
```

This helps identify the distribution name, version, and other operating system details.

### 5.6 pwd

Displays the present working directory.

```bash
pwd
```

Example output:

```text
/home/user
```

This command is useful for identifying your current location in the Linux file system.

---

## 6. How a Linux Command Executes

When a user enters a command in the terminal, the shell interprets the command and requests the required operation from the operating system.

For example:

```bash
pwd
```

### Command Execution Flow

1. The user enters `pwd` in the terminal.
2. Bash, the shell, interprets the command.
3. Bash requests the required information from the operating system through system calls.
4. The kernel provides the necessary information.
5. Bash displays the result in the terminal.

```text
User
 |
 v
Bash Shell
 |
 v
System Call
 |
 v
Linux Kernel
 |
 v
System Information
 |
 v
Terminal Output
```

**Key takeaway:** The shell provides the interface between the user and the operating system, while the kernel manages system resources and hardware.

---

## 7. Linux vs Windows

| Feature             | Linux                                          | Windows                                  |
| ------------------- | ---------------------------------------------- | ---------------------------------------- |
| Source Code         | Open source kernel                             | Proprietary operating system             |
| Customization       | Highly customizable                            | More restricted customization            |
| Common Usage        | Servers, cloud, embedded systems               | Desktops, business applications, gaming  |
| Interface           | CLI and graphical interfaces                   | Primarily GUI focused, with CLI tools    |
| System Requirements | Many distributions support lightweight systems | Requirements vary by version and edition |

Both operating systems support command line tools and graphical interfaces.

Linux is especially common in server and cloud environments, while Windows is widely used on personal and business desktops.

---

## 8. Key Takeaways

* Linux is an open source operating system kernel created by Linus Torvalds in 1991.
* The kernel manages memory, CPU, processes, file systems, and hardware.
* Applications use the kernel to access system resources.
* Linux is widely used in servers, cloud platforms, and cybersecurity.
* Linux distributions combine the kernel with system utilities and software.
* Basic commands such as `uname`, `hostnamectl`, and `pwd` help inspect a Linux system.
* Bash interprets user commands and interacts with the operating system.

## Practical Exercise

Run the following commands in your Linux terminal and observe the output.

```bash
uname
uname -a
hostname
hostnamectl
cat /etc/os-release
pwd
```

### Learning Objective

Understand what Linux is, how the kernel works, where Linux is used, and how to identify basic system information using command line tools.
