# Lesson: Linux Boot Process

## 1. Linux Boot Process

Linux follows these steps during startup:

```text
Power ON
    ↓
BIOS / UEFI
    ↓
GRUB Bootloader
    ↓
Linux Kernel
    ↓
initramfs
    ↓
systemd (PID 1)
    ↓
Login Screen
```

| Stage        | Use                                                      |
| ------------ | -------------------------------------------------------- |
| BIOS / UEFI  | Initializes hardware and finds the boot device           |
| GRUB         | Displays the boot menu and loads the kernel              |
| Kernel       | Manages CPU, memory, and hardware                        |
| initramfs    | Loads essential drivers and prepares the root filesystem |
| systemd      | Starts system services and manages startup               |
| Login Screen | Allows users to log in                                   |

## 2. Linux Boot Commands

| Command                                   | Use                           |
| ----------------------------------------- | ----------------------------- |
| `uname -r`                                | Displays kernel version       |
| `ps -p 1`                                 | Checks process ID 1 (systemd) |
| `systemctl list-units --type=service`     | Lists active service units    |
| `systemctl get-default`                   | Displays default boot target  |
| `systemctl set-default multi-user.target` | Sets command line boot target |
| `systemctl set-default graphical.target`  | Sets graphical boot target    |

## 3. Linux Boot Targets

| Target              | Use                                             |
| ------------------- | ----------------------------------------------- |
| `graphical.target`  | Boots with graphical login                      |
| `multi-user.target` | Boots into command line with multiuser services |

## Practice

```bash
uname -r
ps -p 1
systemctl get-default
systemctl list-units --type=service
```

**Objective:** Understand the Linux boot sequence and use commands to check the kernel, systemd, services, and boot targets.
