# Linux Administration
Motivation is that you'll often have to work with Linux whether you're configuring infrastructure or doing bash scripting as part of a CI/CD pipeline.

## Red Hat Certified System Administrator
Study points for the exam
RHCSA exam candidates should be able to accomplish the tasks below without assistance. These have been grouped into several categories.

Source: https://www.redhat.com/en/services/training/ex200-red-hat-certified-system-administrator-rhcsa-exam?section=objectives

## Understand and use essential tools
- Access a shell prompt and issue commands with correct syntax
- Use input-output redirection (>, >>, |, 2>, etc.)
```
> - overwrites
>> - appends
| - pipes data from one command to the next
2> - writes STDERR to output
```
- Use grep and regular expressions to analyze text
```
# Every line that contains text, ignoring case
grep -i <pattern> <file_or_text>

# Every line that does NOT contain text
grep -v <pattern> <file_or_text>
```

- Access remote systems using SSH
```
# Look at SSH config, suggest changing port and PermitRootLogin = no
cd /etc/ssh
more sshd_config
```
- Log in and switch users in multiuser targets
```
# Change to root
su -

# Switch to another user with their customizations
su - <other_user>
```

- Archive, compress, unpack, and uncompress files using tar, star, gzip, and bzip2
```
# Uncompress with tar
tar -xvf <archive_file>
```
- Create and edit text files
```
# touch or vi
```
- Create, delete, copy, and move files and directories
```
# create
touch newfile.txt  # creates new empty file
vi newfile.txt # create new file for editing

# delete
rm -rf  # removes recursively

# copy
cp <source> <dest>

# move
mv <source> <dest>
```
- Create hard and soft links
- List, set, and change standard ugo/rwx permissions
```
# List files with ls
ls -al

# Change permissions for user (u), group (g), or other (o) --> using +/- with r, w, or x
chmod u+r
```

- Locate, read, and use system documentation including man, info, and files in /usr/share/doc

## Create simple shell scripts
- Conditionally execute code (use of: if, test, [], etc.)
- Use Looping constructs (for, etc.) to process file, command line input
- Process script inputs ($1, $2, etc.)
- Processing output of shell commands within a script

## Operate running systems
- Boot, reboot, and shut down a system normally
- Boot systems into different targets manually
- Interrupt the boot process in order to gain access to a system
- Identify CPU/memory intensive processes and kill processes
- Adjust process scheduling
- Manage tuning profiles
- Locate and interpret system log files and journals
- Preserve system journals
- Start, stop, and check the status of network services
- Securely transfer files between systems

## Configure local storage
- List, create, delete partitions on MBR and GPT disks
- Create and remove physical volumes
- Assign physical volumes to volume groups
- Create and delete logical volumes
- Configure systems to mount file systems at boot by universally unique ID (UUID) or label
- Add new partitions and logical volumes, and swap to a system non-destructively

## Create and configure file systems
- Create, mount, unmount, and use vfat, ext4, and xfs file systems
- Mount and unmount network file systems using NFS
```
# Install packages and start services -- rpcbind, nfs-server, nfs-idmapd
# Share files using /etc/export
# ex. /myshare  *(rw,sync,no_root_squash)
# Export the file system
exportfs -rv
```  
- Configure autofs
- Extend existing logical volumes
- Create and configure set-GID directories for collaboration
```
# change owner
chown <new_owner> <file_or_directory>

# change group
chgrp <new_group> <file_or_directory>

# share files with set-gid, this sets group ownership to the parent directory's group
chmod g+s <file_or_dir>

```
- Diagnose and correct file permission problems

## Deploy, configure, and maintain systems
- Schedule tasks using at and cron
- Start and stop services and configure services to start automatically at boot
```
# Show active or inactive services
systemctl -a

# Start and stop
systemctl start <service>
systemctl stop <service>

# Automatically start or stop with each boot
systemctl enable <service>
systemctl disable <service

# View status of service
systemctl status <service>

# Check listening ports associated to services
netstat -tunlp 
```
- Configure systems to boot into a specific target automatically
- Configure time service clients
```
# Use network time protocol with chronyd
vi /etc/chrony.conf
systemctl restart chronyd
chronyc sources -c
timedatectl set-ntp true
```
- Install and update software packages from Red Hat Network, a remote repository, or from the local file system
```
# List packages
rpm -qa

# Remove package
rpm -e <package>

# Local repo
# add file to /etc/yum.repos.d
# for each entry add: name, baseurl, gpgcheck (0), enabled (1)

# Yum package manager
yum install <package_name>
```
- Modify the system bootloader

## Manage basic networking
- Configure IPv4 and IPv6 addresses
- Configure hostname resolution
- Configure network services to start automatically at boot
- Restrict network access using firewall-cmd/firewall

## Manage users and groups
- Create, delete, and modify local user accounts
```
# View user accounts
cat /etc/passwd
```
- Change passwords and adjust password aging for local user accounts
```
# Shows password policy for user
chage -l <username>

# Shadow file shows password policy values maded thru chage command
cat /etc/shadow

# Has default default settings for password aging
cat /etc/login.defs

# Has many parameters for authentication
cat /etc/pam.d/system-auth
```
- Create, delete, and modify local groups and group memberships
```
# Create group
groupadd <new_group>
cat /etc/group

# Add user and assign group
useradd <username> -G <new_group>
cat /etc/passwd

# Change group for a user
usermod -a -G <group> <user>
```
- Configure superuser access

## Manage security
- Configure firewall settings using firewall-cmd/firewalld
```
# GUI version
firewall-config
cat /etc/firewalld/firewalld.conf

# CLI
firewall-cmd --help

# Older version
iptables --help
```
- Manage default file permissions
- Configure key-based authentication for SSH
```
# shell provides interface to run utilities to interact with kernel
# to secure:
# - set idle timeout
# edit /etc/ssh/sshd_config
ClientAliveInterval 600
# - disable root login
PermitRootLogin no
# - disable empty passwords
PermitEmptyPasswords no
# - limit users' SSH access
AllowUsers user1 user2
#
# Use a different port
Port 22 <-- change this port number
#
# Accessing without password (i.e. SSH-keys)
# keys are generated at user level
# - copy over keys from client to server
# step 1 - ssh-kegen
# step 2 - ssh-copy-id <user>@<server>
```
- Set enforcing and permissive modes for SELinux
```
# Check status of SELinux
sestatus

# To disable SELinux, change to SELINUX=disabled
vi /etc/sysconfig/sysconfig
```
- List and identify SELinux file and process context
```
# View file info
stat <filename>
```
- Restore default file contexts
- Manage SELinux port labels
- Use boolean settings to modify system SELinux settings
- Diagnose and address routine SELinux policy violations

## Manage containers
- Find and retrieve container images from a remote registry
- Inspect container images
- Perform container management using commands such as podman and skopeo
- Build a container from a Containerfile
- Perform basic container management such as running, starting, stopping, and listing running containers
- Run a service inside a container
- Configure a container to start automatically as a systemd service
- Attach persistent storage to a container

As with all Red Hat performance-based exams, configurations must persist after reboot without intervention.

## Other
- OpenLDAP installation
```
# OpenLDAP service is slapd
systemctl start slapd
systemctl enable slapd
/etc/openldap/slapd.d #config file
```
- Networking
```
# Maps journey of packet from source to destination
traceroute
```
- Tuned
```
# For system performance tuning
# Check which profile is active
tuned-adm active

# Check what is recommended
tuned-adm recommend
```
