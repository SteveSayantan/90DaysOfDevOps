# Week 2: Linux System Administration & Automation

Welcome to **Week 2** of the **90 Days of DevOps - 2025 Edition**! This week, we dive into **Linux system administration and automation**, covering essential topics such as **user management, file permissions, log analysis, process control, volume mounts, and shell scripting**.

---

## 🚀 Project: DevOps Linux Server Monitoring & Automation

### **1️⃣ User & Group Management**

#### **Task:**

- Create a user `devops_user` and add them to a group `devops_team`.

  **Solution:**
  ```bash
  sudo useradd -m -G devops_team -s /bin/bash devops_user
  ```
  **Explanation**
  - `-m` flag creates a home directory for the user.
  - `-s` flag is used to specify the default shell of the user.
  - `-G` flag is used to specify a list of **secondary/supplementary** groups which the user is also a member of. Each group is separated from the next by a comma, with no intervening whitespace.

- Set a password and grant **sudo** access.

  **Solution:**
  ```bash
  # setting a password
  sudo passwd dev

  # granting sudo access
  sudo gpasswd -a devops_user sudo
  ```
- Restrict SSH login for certain users in `/etc/ssh/sshd_config`.

  **Solution:**
  
  To restrict SSH login for `devops_user` (say),
  - Run `sudo nano /etc/ssh/sshd_config` to edit the ssh configuration file.

  - Append `DenyUsers devops_user` to the file.

  - Save the file and restart the ssh service using `sudo systemctl restart sshd`
 
---

### **2️⃣ File & Directory Permissions**

#### **Task:**

- Create `/devops_workspace` and a file `project_notes.txt`.

  **Solution:**
  ```bash
  sudo mkdir /devops_workspace

  # changing the ownership of the directory
  sudo chown ubuntu:ubuntu /devops_workspace

  touch /devops_workspace/project_notes.txt
  ```
- Set permissions:
  - **Owner can edit**, **group can read**, **others have no access**.

    **Solution:**
    ```bash
    chmod 640 /devops_workspace/project_notes.txt
    ```
- Use `ls -l` to verify permissions.

  **Solution:**
  ```bash
  ubuntu@ip-172-31-88-216:~$ ls -l /devops_workspace/project_notes.txt
  -rw-r----- 1 ubuntu ubuntu 0 Feb 14 18:48 /devops_workspace/project_notes.txt
  ```
---

### **3️⃣ Log File Analysis with AWK, Grep & Sed**

#### **Task:**

- Download the **Linux_2k.log** file.

  **Solution:**
  - In GitHub, right-click the **Raw** button and copy the link address.
  - Use `wget` to download the file.

  ```bash
  wget https://github.com/logpai/loghub/raw/refs/heads/master/Linux/Linux_2k.log
  ```
- Extract insights using commands:
  - Use `grep` to find all occurrences of **"error"**.
    
    **Solution**
    ```bash
    grep -i "error" Linux_2k.log
    ```
    **Output**: No occurrence found.
  - Use `awk` to extract **timestamps and log levels**.
  
    **Solution:**
    ```bash
    awk '{ 
            msg = "INFO"

            switch ($0) {

                case /authentication fail/:
                    msg = "ERROR"
                    break
                case /ALERT/:
                    msg = "ALERT"
                    break
            };

            print $1,$2,$3,msg

        }' Linux_2k.log;
    ```
    **Output**
    ```
    Jun 14 15:16:01 ERROR
    Jun 14 15:16:02 INFO
    Jun 14 15:16:02 ERROR
    Jun 15 02:04:59 ERROR
    Jun 15 02:04:59 ERROR
    Jun 15 02:04:59 ERROR
    Jun 15 02:04:59 ERROR
    Jun 15 02:04:59 ERROR
    Jun 15 02:04:59 ERROR
    Jun 15 02:04:59 ERROR
    .
    .
    .
    ```

  - Use `sed` to replace all IP addresses with **[REDACTED]**.

    **Solution**
    ```bash
    sed 's \([[:digit:]]\+\(\.\|-\)\)\{3\}[[:digit:]]\+ [REDACTED] g' < Linux_2k.log
    #Output:
    # 211.72.151.162 --> [REDACTED]
    # 82-252-162-81 --> [REDACTED]
    ```
    **Output**:
    ```bash
    Jul 17 23:21:54 combo ftpd[25232]: connection from [REDACTED] ([REDACTED].dsl.in-addr.zen.co.uk) at Sun Jul 17 23:21:54 2005
    Jul 17 23:21:54 combo ftpd[25233]: connection from [REDACTED] ([REDACTED].dsl.in-addr.zen.co.uk) at Sun Jul 17 23:21:54 2005
    Jul 17 23:21:54 combo ftpd[25234]: connection from [REDACTED] ([REDACTED].dsl.in-addr.zen.co.uk) at Sun Jul 17 23:21:54 2005
    Jul 17 23:21:54 combo ftpd[25235]: connection from [REDACTED] ([REDACTED].dsl.in-addr.zen.co.uk) at Sun Jul 17 23:21:54 2005
    Jul 17 23:21:54 combo ftpd[25236]: connection from [REDACTED] ([REDACTED].dsl.in-addr.zen.co.uk) at Sun Jul 17 23:21:54 2005

    ```

---

### **4️⃣ Volume Management & Disk Usage**

#### **Task:**

- Create a directory `/mnt/devops_data`.

  **Solution:**
  ```bash
  sudo mkdir /mnt/devops_data
  ```
- Mount a new volume (or loop device for local practice).

  **Solution:**
  ```bash
  # First, attach a volume (say, /dev/xvdf)

  # formatting the volume with ext4 filesystem before mounting
  sudo mkfs.ext4 /dev/xvdf

  # mounting the volume
  sudo mount /dev/xvdf /mnt/devops_data
  ```
- Verify using `df -h` and `mount | grep devops_data`.

  **Output:**
  ```bash
  ubuntu@ip-172-31-88-216:~$ df -h
  Filesystem      Size  Used Avail Use% Mounted on
  /dev/root       6.8G  1.7G  5.0G  26% /
  tmpfs           479M     0  479M   0% /dev/shm
  tmpfs           192M  884K  191M   1% /run
  tmpfs           5.0M     0  5.0M   0% /run/lock
  /dev/xvda16     881M   76M  744M  10% /boot
  /dev/xvda15     105M  6.1M   99M   6% /boot/efi
  tmpfs            96M   12K   96M   1% /run/user/1000
  /dev/xvdf       2.0G   24K  1.8G   1% /mnt/devops_data
  ubuntu@ip-172-31-88-216:~$ mount | grep devops_data
  /dev/xvdf on /mnt/devops_data type ext4 (rw,relatime)
  ```

---

### **5️⃣ Process Management & Monitoring**

#### **Task:**

- Start a background process (`ping google.com > ping_test.log &`).

  **Output:**
  ```bash
  [1] 1081 # The JOB ID of this process is 1
  ```
- Use `ps`, `top` to monitor it.

  **Output:**
  ```bash
  ubuntu@ip-172-31-88-216:~$ ps
    PID TTY          TIME CMD
   1016 pts/0    00:00:00 bash
   1081 pts/0    00:00:00 ping
   1187 pts/0    00:00:00 ps

  ubuntu@ip-172-31-88-216:~$ top
  top - 06:18:36 up 7 min,  2 users,  load average: 0.00, 0.05, 0.05
  Tasks: 108 total,   1 running, 107 sleeping,   0 stopped,   0 zombie
  %Cpu(s):  0.0 us,  0.0 sy,  0.0 ni, 99.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.3 st
  MiB Mem :    957.4 total,    343.1 free,    310.0 used,    457.0 buff/cache
  MiB Swap:      0.0 total,      0.0 free,      0.0 used.    647.4 avail Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
   1015 ubuntu    20   0   15220   7840   5632 S   0.0   0.8   0:00.09 sshd
   1016 ubuntu    20   0    9060   5248   3584 S   0.0   0.5   0:00.01 bash
   1074 ubuntu    20   0   15000   6948   4992 S   0.0   0.7   0:00.00 sshd
   1075 ubuntu    20   0    2748   1920   1792 S   0.0   0.2   0:00.00 sftp-server
   1081 ubuntu    20   0    8072   2688   2560 S   0.0   0.3   0:00.01 ping
  ```

- Kill the process and verify it's gone.

  **Output:**
  ```bash
  ubuntu@ip-172-31-88-216:~$ jobs
  [1]+  Running                 ping google.com >> ping_test.log &
  ubuntu@ip-172-31-88-216:~$ kill %1  # "kill %n" kills the job with JOB ID n
  ubuntu@ip-172-31-88-216:~$ jobs
  [1]+  Terminated              ping google.com >> ping_test.log
  ```

---

### **6️⃣ Automate Backups with Shell Scripting**

#### **Task:**

- Write a shell script to back up `/devops_workspace` as `backup_$(date +%F).tar.gz`.
- Save it in `/backups` and schedule it using `cron`.

**Solution:**

- `backupScript.sh` for backup

  ```bash
  #! /bin/bash

  # Check to make sure the user has entered exactly two arguments
  if [ $# -ne 2 ]
  then
      echo "Usage: backup.sh <source_dir> <target_dir>"
      echo "Please try again"
      exit 1
  fi

  # Check to make sure the source and destination directory exists
  if [ ! -d $1 -o ! -d $2 ];
  then
    echo "Invalid location"
    exit 2
  fi

  # creating backups
  current_time=$(date +%F_%H-%M)
  $(which tar) -czf $2/backup_$current_time.tar.gz $1 &> /dev/null

  ```
- entry in crontab: to run at 00:00 (midnight) daily
  ```bash
  0 0 * * * sudo /home/ubuntu/backupScript.sh /devops_workspace /var/backups/demo-backups
  ```
  **Example Output**
  ```bash
  ubuntu@ip-172-31-88-216:~$ ls -l /var/backups/demo-backups/
  total 12
  -rw-r--r-- 1 root root 191 Feb 15 00:00 backup_2025-02-15_00-00.tar.gz
  -rw-r--r-- 1 root root 191 Feb 16 00:00 backup_2025-02-16_00-00.tar.gz
  -rw-r--r-- 1 root root 191 Feb 17 00:00 backup_2025-02-17_00-00.tar.gz
  ...
  ```
  **Explanation**

  Flags used with `tar` command:
  - `-c` to create a new tar file.
  - `-z` to compress the tar file using **gzip**.
  - `-f` to specify the name of the tar file.
