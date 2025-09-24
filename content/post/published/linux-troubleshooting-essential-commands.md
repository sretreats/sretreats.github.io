---
title: "Troubleshooting on linux - Essential Commands"
date: 2025-09-23
hero: "/images/posts/linux-know-how-sres.jpg"
excerpt: "Commands that should be in your toolbox for effectively management and troubleshooting on linux"
authors:
  - Hemant Negi
---

## 1. How long this machine is running from?
```bash
❯ uptime
16:35:57 up 10 days,  1:01,  1 user,  load average: 0.00, 0.01, 0.05
```
The output shows the current time, how long the system has been running (`up 10 days`), the number of logged-in users, and the system load averages over the last 1, 5, and 15 minutes. Low load averages like these indicate an idle system.

## 2. Whoami (who am i)
This command prints the effective userid of the current user.
```bash
❯ whoami
root
```
The command shows that the current user is `root`, the superuser.

## 3. Groups
This command displays the groups a user belongs to. or all the groups available on the system.
```bash
❯ groups hemant
hemant : hemant sudo
```
This shows that the user `hemant` is a member of the `hemant` and `sudo` groups.

## 4. Users
This command shows the login names of users currently on the system.
```bash
❯ users
hemant root
```
This lists the users that are currently logged in.

## 5. Which (which binary is executing)
This command shows the full path of the shell command.
```bash
❯ which bash
/bin/bash
```
This indicates that the `bash` executable is located in the `/bin` directory.

## 6. Stat
This command displays file or file system status.
```bash
❯ stat /etc/hosts
  File: /etc/hosts
  Size: 245       	Blocks: 8          IO Block: 4096   regular file
Device: 801h/2049d	Inode: 262145      Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2025-09-23 10:20:00.123456789 +0000
Modify: 2025-09-22 08:00:00.987654321 +0000
Change: 2025-09-22 08:00:00.987654321 +0000
 Birth: -
```
The output provides detailed information about the file, including its size, permissions (`0644`), owner (`root`), and various timestamps (last access, modification, and change).

## 7. Strace
This command traces system calls and signals, which is useful for debugging.
```bash
❯ strace -c ls > /dev/null
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
 25.00    0.000125           5        25           mmap
 15.00    0.000075           4        18           mprotect
 10.00    0.000050           5        10           openat
...
------ ----------- ----------- --------- --------- ----------------
100.00    0.000500                    95           total
```
The `-c` flag provides a summary of system calls made by the `ls` command. It shows the percentage of time spent in each syscall, the total time, and the number of calls. This helps identify performance bottlenecks.

## 8. ip a
This command displays network device information.
```bash
❯ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:7a:1b:7c brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic eth0
       valid_lft 86354sec preferred_lft 86354sec
```
This shows all network interfaces. `lo` is the local loopback interface. `eth0` is the primary network interface, with the IP address `10.0.2.15`.

## 9. df
This command displays disk free space in a human-readable format.
```bash
❯ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        40G  3.8G   34G  11% /
tmpfs           1.9G     0  1.9G   0% /dev/shm
```
The output shows that the root filesystem (`/`) is 40GB in size, with 3.8GB used, leaving 34GB available (11% used).

## 10. du
This command estimates file space usage.
```bash
❯ du -sh /var/log
25M	/var/log
```
The `-s` flag summarizes the total size, and `-h` makes it human-readable. This tells us the `/var/log` directory is using 25MB.

To display the top 5 largest directories in the current directory:
```bash
❯ du -h * | sort -rh | head -5
1.2G	var
500M	usr
150M	home
25M	etc
5.0M	boot
```
This command combination is powerful. `du -h *` gets the size of each file/directory, `sort -rh` sorts them in reverse (largest first) human-readable order, and `head -5` shows the top 5.


## 11. ps
This command reports a snapshot of the current processes.
```bash
❯ ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.1  22536  5784 ?        Ss   Sep22   0:02 /sbin/init
root         936  0.0  0.2  75860  8144 ?        Ss   Sep22   0:00 /lib/systemd/systemd-journald
shemant     1234  0.2  1.5 876543 45678 ?        Sl   11:30   0:55 /usr/bin/gnome-shell
```
This provides a list of all running processes, showing the user, Process ID (PID), CPU and memory usage, and the command that started it.

## 12. Netstat
This command displays network connections, routing tables, and interface statistics. The options `-tuln` show active TCP, UDP, and listening sockets numerically.
```bash
❯ netstat -tuln
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
tcp6       0      0 :::80                   :::*                    LISTEN
```
The output shows that a process is listening for TCP connections on port 22 (SSH) and another is listening on port 80 (HTTP) for IPv6 connections.

## 13. Lsof
This command lists open files. Using `-i :<port>` will show which process is using a specific network port.
```bash
❯ lsof -i :22
COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sshd     1158 root    3u  IPv4  20935      0t0  TCP *:ssh (LISTEN)
```
This shows that the `sshd` process with PID 1158 is listening on the SSH port. This is great for identifying which application is using a particular port.

## 14. Top/Htop
These commands display Linux processes in real-time. `htop` is an interactive and more user-friendly version of `top`.
```bash
❯ top
top - 17:15:20 up 11 days, 1:41,  1 user,  load average: 0.01, 0.02, 0.00
Tasks: 124 total,   1 running, 123 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.1 us,  0.1 sy,  0.0 ni, 99.8 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :   3934.3 total,   2345.8 free,    876.5 used,    712.0 buff/cache
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   2854.7 avail Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
   1234 shemant   20   0  876543  45678  23456 S   0.7   1.1   0:55.55 gnome-shell
      1 root      20   0   22536   5784   3456 S   0.0   0.1   0:02.11 init
```
The header provides a system summary (uptime, load, tasks, CPU, memory). The table below lists processes sorted by CPU usage, allowing you to quickly identify resource-intensive applications.

## 15. Grep
This command searches for patterns in files. It's invaluable for finding specific text within logs or configuration files.
```bash
❯ grep "error" /var/log/syslog
Sep 23 10:30:01 my-server kernel: [    0.123456] ACPI: SPCR: Unexpected SPCR Access Width.  Defaulting to byte size
Sep 23 11:45:10 my-server anacron[4567]: Anacron job 'cron.daily' terminated (exit status 1) (produced error output)
```
`grep` has printed every line from `/var/log/syslog` that contains the word "error". This is extremely useful for filtering large log files to find relevant entries.

## 16. Sed
This command is a stream editor for filtering and transforming text. It can perform substitutions, deletions, and insertions on text.
```bash
❯ sed 's/root/admin/g' /etc/passwd | grep admin
admin:x:0:0:admin:/root:/bin/bash
```
This command replaces all occurrences of "root" with "admin" in the `/etc/passwd` file and prints the modified lines. `sed` is a powerful tool for scripting bulk text transformations.

## 17. Awk
This command is a powerful pattern scanning and processing language, often used for text processing and data extraction.
```bash
❯ awk -F: '{print $1}' /etc/passwd
root
daemon
bin
sys
```
This command splits each line of `/etc/passwd` by the colon (`:`) delimiter and prints the first field, which is the username. `awk` excels at processing structured text data.

## 18. Curl
These commands are used to transfer data from or to a server.
```bash
❯ curl -I https://www.google.com
HTTP/2 200
content-type: text/html; charset=ISO-8859-1
p3p: CP="This is not a P3P policy! See g.co/p3phelp for more info."
date: Tue, 23 Sep 2025 17:20:00 GMT
server: gws
```
The `-I` flag tells `curl` to fetch only the HTTP headers. The `HTTP/2 200` status indicates a successful request. Headers provide metadata about the response.

## 19. Rsync
This command is a fast, versatile, remote (and local) file-copying tool, efficient for synchronizing files and directories.
```bash
❯ rsync -avz --progress ./source-dir/ user@remote-host:/destination-dir/
sending incremental file list
source-dir/
source-dir/file1.txt
        12,345 100%    0.00kB/s    0:00:00 (xfr#1, to-check=2/4)
source-dir/file2.txt
        54,321 100%   50.00MB/s    0:00:00 (xfr#2, to-check=1/4)
...
sent 67,123 bytes  received 92 bytes  13,443.00 bytes/sec
total size is 66,666  speedup is 0.99
```
`rsync` is copying files from a local source directory to a remote destination. It shows the progress of each file transfer and a summary at the end. The `-avz` flags stand for archive mode, verbose, and compress, respectively.

## 20. Ssh-keygen
This command generates, manages, and converts authentication keys for SSH, which is crucial for secure remote access.
```bash
❯ ssh-keygen -t rsa -b 4096
Generating public/private rsa key pair.
Enter file in which to save the key (/home/shemant/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/shemant/.ssh/id_rsa.
Your public key has been saved in /home/shemant/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:AbCdEfGhIjKlMnOpQrStUvWxYz1234567890abc shemant@my-server
The key's randomart image is:
+---[RSA 4096]----+
|      .o+=*B.    |
|     . oE=.o .   |
|      o ..o .    |
|       . . .     |
|        S .      |
|       . .       |
|      . .        |
|     . .         |
|    ...          |
+----[SHA256]-----+
```
This has created a new 4096-bit RSA key pair for SSH authentication. The private key is stored in `id_rsa` and the public key in `id_rsa.pub`. The public key can be copied to remote servers to allow passwordless login.

## 21. Chmod/Chown
These commands are used to change file permissions (`chmod`) and file ownership (`chown`), essential for managing access control.
```bash
❯ chmod 755 script.sh
❯ chown shemant:users script.sh
❯ ls -l script.sh

-rwxr-xr-x 1 shemant users 1024 Sep 23 17:30 script.sh
```
The output of `ls -l` now shows that `script.sh` has read, write, and execute permissions for the owner (`shemant`), and read/execute permissions for the group (`users`) and others. The owner and group have also been set accordingly.

## 22. Journalctl
This command queries the `systemd` journal. It's used to view logs from services and the system itself.
```bash
❯ journalctl -u sshd -n 5 --no-pager
-- Logs begin at Tue 2025-09-22 08:00:00 UTC, end at Tue 2025-09-23 17:35:00 UTC. --
Sep 23 16:00:01 my-server sshd[1234]: Server listening on 0.0.0.0 port 22.
Sep 23 16:00:01 my-server sshd[1234]: Server listening on :: port 22.
Sep 23 16:05:10 my-server sshd[5678]: Accepted publickey for shemant from 192.168.1.100 port 54321 ssh2: RSA SHA256:AbCdEf...
Sep 23 16:05:10 my-server sshd[5678]: pam_unix(sshd:session): session opened for user shemant by (uid=0)
Sep 23 16:10:20 my-server sshd[5678]: Received disconnect from 192.168.1.100 port 54321:11: disconnected by user
```
This shows the last 5 log entries for the `sshd` service. You can see when the service started listening, successful logins, and disconnections. `journalctl` is a powerful tool for modern systemd-based Linux distributions.

