---
title: Linux for administrators 
date: 2021-04-05
excerpt: linux administrators
featured: true
authors:
  - Hemant Negi
---


## User/Group/Permission Management in Linux
- Users in Linux has an associated user ID called UID attached to them.
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


```bash
uname
id
uptime
groups
users
```



Memory Management
In this section, we will study about some useful commands that can be used to view information about the system memory.

free
The free command is used to display the memory usage of the system. The command displays the total free and used space available in the RAM along with space occupied by the caches/buffers.



free command by default shows the memory usage in kilobytes. We can use an additional argument to get the data in human-readable format.



vmstat
The vmstat command can be used to display the memory usage along with additional information about io and cpu usage.



Checking Disk Space
In this section, we will study about some useful commands that can be used to view disk space on Linux.

df (disk free)
The df command is used to display the free and available space for each mounted file system.



du (disk usage)
The du command is used to display disk usage of files and directories on the system.



The below command can be used to display the top 5 largest directories in the root directory.



Daemons
A computer program that runs as a background process is called a daemon. Traditionally, the name of daemon processes ended with d - sshd, httpd etc. We cannot interact with a daemon process as they run in the background.

Services and daemons are used interchangeably most of the time.

Systemd
Systemd is a system and service manager for Linux operating systems. Systemd units are the building blocks of systemd. These units are represented by unit configuration files.

The below examples shows the unit configuration files available at /usr/lib/systemd/system which are distributed by installed RPM packages. We are more interested in the configuration file that ends with service as these are service units.



Managing System Services
Service units end with .service file extension. Systemctl command can be used to start/stop/restart the services managed by systemd.

Command	Description
systemctl start name.service	Starts a service
systemctl stop name.service	Stops a service
systemctl restart name.service	Restarts a service
systemctl status name.service	Check the status of a service
systemctl reload name.service	Reload the configuration of a service
Logs
In this section, we will talk about some important files and directories which can be very useful for viewing system logs and applications logs in Linux. These logs can be very useful when you are troubleshooting on the system.

