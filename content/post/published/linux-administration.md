---
title: "Linux Administration Essentials"
date: 2025-09-23
hero: "/images/posts/linux-administration.png"
excerpt: "A comprehensive guide covering essential Linux commands for memory, disk, and service management, along with log analysis techniques for system administrators."
authors:
  - Hemant Negi
---



## User/Group/Permission Management in Linux
- Users in Linux has an associated user ID called UID.
- A group is a collection of one or more users. A group makes it easier to share
  permissions among a group of users.
- Each group has a group ID called GID associated with it.
- Users can get sudo access if added to a sudo group in `/etc/sudoers`

**Below files are of importance here** 
### 1. `/etc/passwd` 
Stores the user name, the uid, the gid, the home directory, the login shell etc
```bash
~ ❯ cat /etc/passwd
nobody:x:-2:-2:Unprivileged User:/var/empty:/usr/bin/false
root:x:0:0:System Administrator:/var/root:/bin/sh
daemon:x:1:1:System Services:/var/root:/usr/bin/false
```
Each field in a passwd entry is separated with ":" colon characters, and are as 
follows:
- Username, up to 8 characters. Case-sensitive, usually all lowercase.
- An "x" in the password field. Passwords are stored in the `/etc/shadow` file.
- Numeric user id. This is assigned by the `adduser` script. Unix uses this 
  field, plus the following group field, to identify which files belong to the 
  user.
- Numeric group id. Red Hat uses group id's in a fairly unique manner for 
  enhanced file security. Usually the group id will match the user id.
- Full name of user, can be a large name (30 chars). 
- User's home directory. Usually /home/username.
- Path to user's "shell account" this will be default shell for the user.

### 2. `/etc/shadow`
Stores the password associated with the users.
```bash
~ ❯ cat /etc/shadow
linuxize:$6$zHvrJMa5Y690smbQ$z5zdL...:18009:0:120:7:14::
smithj:Ep6mckrOLChF.:10063:0:99999:7:::
```
Each entry in this file is separated by : there are 9 data points here. 
1. username
1. password encrypted with SHA-512 (the password is truncated for better readability).
1. password last changed date (number of days since epoch.)
1. password expiry age in days. 0 means no expiry.
1. password must be changed after days.
1. user will receive a warning message days before the password expiration date.
1. If the user doesn’t attempt to login to the system 14 days after the password
   is expired, the account will be disabled.
1. account expiration date
1. ignored for future use

### 3. `/etc/group` 
Stores information about different groups on the system
```bash
~ ❯ cat /etc/group
certusers:x:29:root,_jabber,_postfix,_cyrus,_calendar,_dovecot
wheel:x:0:root
```
Each row in this file has 4 data points separated with :
1. Group name - commands like ls reads group name from here.
1. Password - Generally password is not used, hence it is empty/blank.
1. Group ID (GID)
1. Group List - List of usernames member of this group. 


**Commands used to perform actions on users/groups**
| command | description | example |
|---------|-------------|---------|
| useradd | Creates a new user | `useradd hdev -s /bin/zsh -G sre` |
| passwd  | Adds/modifies passwords interactively | `passwd hdev` |
| usermod | Modifies attributes of user | `usermod hdev -s /bin/bash`|
| userdel | Deletes an user | `userdel hdev` |
| groupadd | Creates new group | `groupadd sre` |
| groupmod | Modifies attributes of a group | `groupmod sre -n new-name` |
| groupdel | Deletes a group | `groupdel sre` |

### 4. `/etc/sudoers`
This file contains the name of groups whose users can invoke sudo. Also contains
rules that users must follow when running the sudo command. For example, root 
is allowed to run any commands from anywhere. 
```bash
~ ❯ sudo cat /etc/sudoers
# root and users in group wheel can run anything on any machine as any user
root		ALL = (ALL) ALL
```

We can add a user to `root` group to grant him sudo access. eg.
`usermod -a -G root hdev`

### 5. File permissions
In a linux based OS, each file and directory is assigned access permissions for 
the owner of the file, the members of a group of related users and everybody 
else(others).

File permissions, size, user, group etc are stored in Inodes. 
```bash
~ ❯ l ~/projects
drwxr-xr-x    4 hdev  staff   128B Apr  4  2019 K8s Deployables
```
lets break down those rows and see what information it represents.
| Char  | Description |
|-------|-------------|
| `d`   | first char denotes file type. `d` is for directory `-` is for regular files |
| `rwx` | next three char denotes the file owners permission as read(r), write(w), execute(x) |
| `r-x` | these denotes permissions for groups members (read and execute) |
| `r-x` | these denotes permissions for all other users (read and execute) |
| `hdev` | is the owner's username |
| `staff` | Name of group owner of file |

We can think of permission as a series of bits with 1 representing True or 
allowed and 0 representing False or not allowed.

| Permission | rwx | Binary | Decimal |
|------------|-----|--------|---------|
| Read, write and execute	| `rwx`	| 111	| 7 |
| Read and write	| `rw-`	| 110	| 6 |
| Read and execute	| `r-x`	| 101	| 5 |
| Read only	| `r--`	| 100	| 4 |
| Write and execute	| `-wx`	| 011	| 3 |
| Write only	| `-w-`	| 010	| 2 |
| Execute only	| `--x`	| 001	| 1 |
| None	| `---`	| 000	| 0 |

Change these attributes of a file as 
```bash
❯ chmod 644 test_file.py
❯ ls -l test_file.py
-rw-r--r--  1 hdev  staff  0 Apr  6 11:51 test_file.py
❯ # can use it like below
❯ chmod u=rwx test_file.py  # set user to rwx
❯ chmod g=-w- test_file.py  # set group to -w-
❯ chmod o=-w- test_file.py  # set others to -w-
❯ chmod a=-w- test_file.py  # set all to -w-
❯ sudo chown root test_file.py          # Change owner to root
❯ sudo chown root:admin test_file.py    # change owner anc group both
❯ sudo chgrp admin test_file.py         # cahnge group to admin
```

### 6. Managing remote machines

- SSH login to a remote machine
    ```bash 
    ssh -i /path/to/privatekey username@42.12.23.3
    ```
- Remote command execution via ssh
    ```bash 
    ssh -i /path/to/privatekey username@42.12.23.3 'ps aux | less'
    ```
- Copy file to remote
    ```bash 
    scp -i /path/to/privatekey sample_file username@42.12.23.3:/path/in/remote/sample_file
    ```

### 7. Process mangement
- List running processes by `ps` (process status) and using `top` 
    ```bash
    ❯ ps aux
    ❯ ps -i 23
    ❯ top      
    ```



## Memory Management
In this section, we will study about some useful commands that can be used to view information about the system memory.

### free
The `free` command is used to display the memory usage of the system. The command displays the total free and used space available in the RAM along with space occupied by the caches/buffers. By default, it shows the memory usage in kilobytes, but using the `-h` flag makes it human-readable.

```bash
❯ free -h
              total        used        free      shared  buff/cache   available
Mem:          3.8Gi       1.1Gi       1.3Gi        12Mi       1.4Gi       2.4Gi
Swap:         2.0Gi          0B       2.0Gi
```
This shows a system with 3.8GB of RAM, with 1.1GB used and 2.4Gi available for new applications.

### vmstat
The `vmstat` command can be used to display the memory usage along with additional information about I/O and CPU usage.

```bash
❯ vmstat 1 5
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 1353192 181376 1284784    0    0     8    25   123  211  1  1 98  0  0
 0  0      0 1353192 181376 1284784    0    0     0     0   140  240  0  0 100 0  0
 0  0      0 1353192 181376 1284784    0    0     0     0   130  225  0  0 100 0  0
 0  0      0 1353192 181376 1284784    0    0     0     0   128  221  0  0 100 0  0
 0  0      0 1353192 181376 1284784    0    0     0     0   135  231  0  0 100 0  0
```
This command shows statistics every second for 5 seconds. The columns provide information about processes (`procs`), memory, swap, I/O, system, and CPU activity. It's useful for a quick glance at system performance.


## Systemd
Systemd is a system and service manager for Linux operating systems. Systemd units are the building blocks of `systemd`. These units are represented by unit configuration files located in directories like `/usr/lib/systemd/system`. The configuration files that end with `.service` are of particular interest as these define services.

### Managing System Services
Service units end with a `.service` file extension. The `systemctl` command can be used to start, stop, and restart the services managed by `systemd`.

| Command                      | Description                                |
| ---------------------------- | ------------------------------------------ |
| `systemctl start name.service` | Starts a service                           |
| `systemctl stop name.service`  | Stops a service                            |
| `systemctl restart name.service` | Restarts a service                         |
| `systemctl status name.service`  | Check the status of a service              |
| `systemctl reload name.service`  | Reload the configuration of a service      |
| `systemctl enable name.service`  | Enable a service to start on boot        |
| `systemctl disable name.service` | Disable a service from starting on boot    |

For example, to check the status of the SSH daemon:
```bash
❯ systemctl status sshd
● sshd.service - OpenSSH server daemon
     Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2025-09-23 08:00:00 UTC; 1 weeks 1 days ago
...
```
The output shows the service is `loaded`, `enabled` (starts on boot), and `active (running)`.

## Logs
In this section, we will talk about some important files and directories which can be very useful for viewing system logs and applications logs in Linux. These logs can be very useful when you are troubleshooting on the system.

### Key Log Files and Directories
*   `/var/log/syslog` or `/var/log/messages`: Contains global system logs.
*   `/var/log/auth.log` or `/var/log/secure`: Contains authentication logs.
*   `/var/log/dmesg`: Contains kernel ring buffer messages. You can also view this with the `dmesg` command.
*   Application-specific logs like `/var/log/nginx/` or `/var/log/apache2/`.

### Using `journalctl`
On modern systems using `systemd`, `journalctl` is the primary tool for querying logs.

To view all logs, with the newest entries at the bottom (you can navigate with arrow keys):
```bash
❯ journalctl
```

To follow logs in real-time:
```bash
❯ journalctl -f
```

To view logs for a specific service:
```bash
❯ journalctl -u sshd
-- Logs begin at Tue 2025-09-22 08:00:00 UTC, end at Tue 2025-09-23 17:35:00 UTC. --
Sep 23 16:00:01 my-server sshd[1234]: Server listening on 0.0.0.0 port 22.
...
Sep 23 16:05:10 my-server sshd[5678]: Accepted publickey for shemant from 192.168.1.100 port 54321 ssh2: RSA SHA256:AbCdEf...
```
This command shows all log entries for the `sshd` service, which is very useful for debugging service-specific issues.

