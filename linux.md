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

# matches Car, car, Cat
grep "[Cc]a[RrTt]" <file_to_search>

# matches over a range of characters
grep "da[a-z]" <file_to_search>

# match beginning of a line
grep "^He" <file_to_search>
ls -l | grep "^d"

# inverted match -- doesn't match characters a through i
grep "[d[^a-i]e" <file_to_search>

# end of line  -- matches phrase at end of line
grep "got$"

# any one character -- this matches "stupid"
grep "st..id" <file_to_search>

# escape character, matches the word out with a full stop
grep "out\.$" <file_to_search>

# match 0 or more of the previous character, this would match 
grep "hap*"

# matches the any(.) zero or more times
grep "hap.*ed" <file_to_search>
```

- Access remote systems using SSH
```
# Look at SSH config, suggest changing port and PermitRootLogin = no
cd /etc/ssh
more sshd_config

# remote with basic auth
ssh <user_name>@<remote_host>

# can tee the output
ssh <user_name>@<remote_host> | tee ssh-output.log

# public key auth
ssh-keygen -t rsa
ssh-copy-id <user_name>@<remote_host>
```
- Log in and switch users in multiuser targets
```
# 6 run levels
# 0 - halt system
# 1 - single user mode
# 2 - multi user mode without networking
# 3 - full multiuser mode (no GUI)
# 4 - unused
# 5 - x11 display (GUI)
# 6 - reboot system

# show current runlevel
runlevel

# switch level
init <runlevel>

# Change to root
su -

# Switch to another user with their customizations
su - <other_user>
```

- Archive, compress, unpack, and uncompress files using tar, star, gzip, and bzip2
```
# gzip and bzip used to compress single file
# compress with gzip
gzip <filename>

# extract with gzip
gzip -d <filename.gz>

# bzip compress
bzip2 <file>

# bzip extract
bzip2 -d <file.bz2>

# compress with tar -- compresses multiple files
# compress, verbose
tar cvf <name_of_tar> <file_to_compress>

# compress with tar and gzip
tar cvzf <compressed.tar.gz> <file_to_compress>

# compress with tar and bzip
tar cvjf <compressed.tar.bz2> <file_to_compress>

# list contents of tar
tar -tf <compressed.tar>

# Uncompress with tar
tar -xvf <archive_file>

# Uncompress tar with gzip
tar -xvzf <archive_file.tar.gz>

# Uncompress  tar with bzip
tar -xvjf <archive_file.tar.bz2>

# star is used for SELINUX
yum install star

# compress with star
star -c -f=file1.star <file_or_directory_to_compress>

# extended attributes
star -xattr -H=exustar -c -f=<example.star> <file_to_compress>

# list files in start
star -t -f=example.star

# extract star
star -x -f=example.star
```
- Create and edit text files
```
# touch or vi
# go into command mode with ESC
# "i" for insert mode
# :wq # write file and quit
# "x" to delete
# "u" to undo
# "dw" to delete word
# "2dw" deletes 2 words
# "dd" delete line
# "r" to replace
# "cw" change word
# "/" to search, "n" to go to next result
```
- Create, delete, copy, and move files and directories
```
# create file
touch newfile.txt  # creates new empty file
vi newfile.txt # create new file for editing

# create directory
mkdir <directory_name>
mkdir -p <dir1>/<dir2>/<dir3> # to create directories that don't exist

# delete file
rm <file>

# delete folder
rm -rf  # removes recursively with force

# copy file
cp <source> <dest>

# copy directory
cp -r <source> <dest>

# move
mv <source> <dest>
```
- Create hard and soft links
```
# create hard link
# can only be in same filesystem
# cannot link directories
# same inode number and permissions of original
ln <path_to_file>

# create symbolic link
# can cross between file systems, allows linking directories
# has new inode number and file permissions
# has only path of original file, not contents
ln -s <path_to_file>
```
- List, set, and change standard ugo/rwx permissions
```
# List files with ls
ls -al

# Change permissions for user (u), group (g), or other (o) --> using +/- with r, w, or x
chmod u+r
```

- Locate, read, and use system documentation including man, info, and files in /usr/share/doc
```
man <command>
info <command>
```

## Create simple shell scripts
- Conditionally execute code (use of: if, test, [], etc.)
```
# Make sure to leave spaces around the square brackets for it to evaluate
#!/bin/bash

if [ "$(uname)" == "Linux" ]
then 
    echo "OK"
else 
    echo "nah"
fi
```
```
#!/bin/bash

if test "$(uname)" == "Linux"
then 
    echo "OK"
else 
    echo "nah"
fi
```
```
#!/bin/bash

if [ -e ~/file.txt ]
then 
    echo "File exists"
else 
    echo "File does not exist"
fi
```

- Use Looping constructs (for, etc.) to process file, command line input
```
## WHILE LOOP
#!/usr/bin/bash
COUNTER=1
while [ "$COUNTER" -lt 10 ]
do
    echo "The counter is $COUNTER."
    ((COUNTER++))
done

## UNTIL
until [ $COUNTER -eq 0 ]
do
   echo "The counter is $COUNTER."
   ((COUNTER--))
done
```
```
# FOR LOOP - uses subshell to feed in file
## IFS - is the internal file separator
OLDIFS=$IFS
IFS=$'\n'

for i in $(cat /tmp/testusers.csv)
do
   echo "$i"
done


```
- Process script inputs ($1, $2, etc.)
```
Shell scripts have access to some "magic" variables from the environment:

$0 - The name of the script
$1 - The first argument sent to the script
$2 - The second argument sent to the script
$3 - The third argument... and so forth
$# - The number of arguments provided
$@ - A list of all arguments provided
#!/bin/bash

if [ $# -eq 0 ];
then
  echo "$0: Missing arguments"
  exit 1
elif [ $# -gt 2 ];
then
  echo "$0: Too many arguments: $@"
  exit 1
else
  echo "We got some argument(s)"
  echo "==========================="
  echo "Number of arguments.: $#"
  echo "List of arguments...: $@"
  echo "Arg #1..............: $1"
  echo "Arg #2..............: $2"
  echo "==========================="
fi

echo "And then we do something with $1 $2"
```
```
# Looping through arguments
#!/bin/bash

echo "We received $# argument(s)"

for i in "$@"
do
  echo "Arg...: $i"
done
```
- Processing output of shell commands within a script
```
# Use command substitution with parentheses. Can store as variable.
SERVERNAME=$(hostname)
echo "Running command @ $SERVERNAME...."
```
## Operate running systems
- Boot, reboot, and shut down a system normally
```
# Reboot
sudo shutdown -r now
sudo systemctl reboot

# Shutdown
sudo shutdown --halt +5
```
- Boot systems into different targets manually
```
# Docs
man runlevel

# List targets
systemctl list-units --type target

# Current target
systemctl get-default

# Change default target
systemctl set-default <target_name>
reboot

# Change manually with isolate
systemctl isolate <target_name>
```

- Interrupt the boot process in order to gain access to a system
```
# Press 'e' from the GRUB menu
# add rd.break parameter
# this puts system in emergency mode and you can do things like recover password
chroot /sysroot
passwd root
# for SELinux relabeling if you've made system changes
ctouch /.autorelabel
```

- Identify CPU/memory intensive processes and kill processes
```
# Instantaneous view of system, also has memory, cpu
top

# Process details
ps -edf

# Kill process
## Get PID from top or ps
kill [OPTION] PID

## List of options
kill -l
kill -9 # forceful
kill -15 # graceful
```

- Adjust process scheduling
```
# ctrl-z to stop process
bg # bring process to background
fg # bring process to foreground
jobs # look at jobs
nohup <process> & # run process even after exit

# Nice priority goes from -20 to 19 with lowest being high priority
nice -n <priority_value> <process>
```
  
- Manage tuning profiles
```
# For system performance tuning
# Check which profile is active
tuned-adm active

# Check what is recommended
tuned-adm recommend

# List profiles
tunded-adm list

# Change profile
tuned-adm profile <profile>
```

- Locate and interpret system log files and journals
```
# By default located in /var/log based on config in /etc/rsyslog.conf
# ...can cat or tail these logs
# /var/log/messages is primary place to look

# Alternative is to use journalctl
journalctl -n <num_of_messages>
journalctl -p <priority_eg_crit>
journalctl -u <service_name>
```
- Preserve system journals
```
# Edit config
vim /etc/systemd/journal.conf

# Set storage to persistent
[Journal]
Storage=persistent

# Restart service
systemctl restart systemd-journald
```
- Start, stop, and check the status of network services
```
systemctl start <service>
systemctl list-units --type=service
systemctl stop <service>
systemctl status <service>
systemctl enable <service> # start at boot
systemctl disable <service>
systemctl restart <service
```

- Securely transfer files between systems
```
# Secure copy uses SSH to transfer file, use same credentials
scp file1 user@192.268.1.3:/home/user

# If private key at nonstandard location or port different
scp -i /home/keys/id_rsa -P 2390 file1 user@192.268.1.3:/home/user

# SFTP is another option
sftp user@192.168.1.3
# then use get or put to download/upload file

# Rsync can be used to sync files on same computer or from remote system efficiently
# faster than ftp and scp
rsync [options] [source] [destination]
```

## Configure local storage
- List, create, delete partitions on MBR and GPT disks
```
# List partitions
fdisk -l
lsblk -a

# Create partition
fdisk <name_of_disk>  # e.g. /dev/sdb
# use "n" to create new partition and follow prompts
# write it to disk with "w"
# format file system
mkfs.xfs /dev/sdb1
# mount
mkdir /data
mount /dev/sdb1/ /data
# check
df -h
# make persistent
vim /etc/fstab
# use partprobe to make sure system registers changes

# Delete partition
Same process as creating but select "d" to delete
Then use partprobe to register changes

# GPT disks are similar, use gdisk instead of fdisk command
```
- Create and remove physical volumes
```
# Udemy lecture 188, 190, 191
# NOTE: can use "lvm" to view all LVM commands
# First need to add a disk, such as with VirtualBox > Settings > Storage
# Create new partition and change to type "t" of LVM which is hex "8e"
pvcreate <partition_name>

# Remove physical volume
pvremove <partition_name>

# List physical volumes
pvs
```
- Assign physical volumes to volume groups
```
# Udemy lecture 190
# Create volume group
vgcreate <volume_group_name> <physical_volume_name>

# Display volume group info
vgdisplay <volume_group_name>
```
- Create and delete logical volumes
```
# Udemy lecture 190
lvcreate -n <logical_volume_name> --size <size> <volume_group>

# Display logical volumes
lvs

# Delete logical volume
lvremove <volume_group>/<logical_volume>

# Assign filesystem
mkfs.xfs /dev/<volume_group>/<logical_volume> # can look up this path using lvdisplay

# Mount volume
mkdir /<dir_name>
mount <logical_voume> <mount_path>
```
- Configure systems to mount file systems at boot by universally unique ID (UUID) or label
```
# Get the UUID
blkid

# Update /etc/fstab
UIUID=....    <mount_path>    <file_system>    defaults    0    0

# For label, set the label
e2label <device> label

# Then update /etc/fstab
LABEL=....
```
- Add new partitions and logical volumes, and swap to a system non-destructively
```
# Udemy lecture 191, 192
# Extend LVM by adding disk
## Check current partitions
df -h

## check PV
pvdisplay

## Check VG
vgdisplay

## Create physical volume
pvcreate /dev/sdc1

## Extend the Volume Group
vgextend <vg_name> <pv_to_extend_with>

## Extend the Logical Volume
lvextend --size +1GB <lv_to_extend> # find lv from df -h

# Swap
## Used when RAM is full, has slower access time than physical memory
dd
mkswap
swapon
swapoff

# Look at memory
free -m

# Create swap placeholder
dd if=/dev/zero of=/newswap bs=1M count=1024

# Update file permissions
chmod 600 <file>

# Make swap
mkswap <name_of_swap>

# Enable swap
swapon <name_of_swap>

# Add to fstab
<swap_path>    swap    swap    defaults 0    0
```

## Create and configure file systems
- Create, mount, unmount, and use vfat, ext4, and xfs file systems
```
# Create filesystem
mkfs.xfs <volume>
mkfs.ext4 <volume>
mkfs.vfat <volume>

# Mount
mount <volume> <mount_point>

# Unmount
umount <mount_point_or_device>
```
- Mount and unmount network file systems using NFS
```
# Udemy lecture 197
# Install packages and start services -- rpcbind, nfs-server, nfs-idmapd
# Share files using /etc/export
# ex. /myshare  *(rw,sync,no_root_squash)
# Export the file system
exportfs -rv

# For client, nfs-utils and rpcbind
# Mount the filesystem
mount -t nfs -o options host:/remote/export /local/directory
```  
- Configure autofs
```
# Used to mount and unmount filesystems (e.g. NFS) automatically, alternative to fstab
# https://docs.oracle.com/en/learn/autofs_linux8/#create-mount-point

## Install package
yum install nfs-utils autofs

## Create mount point
sudo mkdir /nfs-mount

## Edit Master Map file: /etc/auto.master
echo "/nfs-mount  /etc/auto.mynfs  --timeout=180" | sudo tee /etc/auto.master.d/mynfs.autofs > /dev/null
# This entry defines the mount point as /nfs-mount and the map file as auto.mynfs.

## Create Map file
echo "mynfs  -fstype=nfs,rw,soft,intr  <SERVER_IP_ADDRESS>:/nfs-share" | sudo tee /etc/auto.mynfs > /dev/null
Where:
- mynfs is a mount point.
-fstype=nfs is the file system type,
and rw,soft,intr are mount options.
<SERVER_IP_ADDRESS> is the IP address of the server instance hosting the NFS server.
:/nfs-share is the NFS share.

## Start service
sudo systemctl enable --now autofs
```

- Extend existing logical volumes
```
lvextend [options] [logical_volume]
```
  
- Create and configure set-GID directories for collaboration
```
# Put users in same group
# usermod -a -G rocketengineers john
# usermod -a -G rocketengineers sarah

# Set group owner for folder
chown -R :rocketengineers /student_projects/rocket_science

# NOTE: use "stat" to look at access details

# share files with set-gid, this sets group ownership to the parent directory's group
chmod g+s <file_or_dir>
```
- Diagnose and correct file permission problems
```
TBD
```

## Deploy, configure, and maintain systems
- Schedule tasks using at and cron
```
# Crontab file is system-wide and for each user
$ ls -l /etc/crontab
$ ls -l /etc/cron.d/

# Check for scheduled jobs
crontab -l

# Edit crontab for user
crontab -e

# Crontab syntax -- minute, hour, day of month, month, day of week

# "at" runs things once in the future
## Show current jobs
at -l

## Run script
at 15:52 -f ./myscript.sh
```
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
```
# View targets
systemctl list-units --type target

# Set the default target
systemctl set-default multi-user.target
```
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
```
grub2-editenv list
grub2-set-default 1 # sets the kernel to 2nd option

# Modify config and then run make
vi /etc/default/grub
bur2-mkconfig -o /boot/grub2/grub.cfg
```

## Manage basic networking
- Configure IPv4 and IPv6 addresses
```
# Udemy lecture 144

# Look at IP info
ip addr

# Network Manager CLI
nmcli connection show
nmcli device status
nmcli connection add
nmcli connection modify
```
- Configure hostname resolution
```
# display hostname
hostname

# edit hostname
vi /etc/hostname # hostname.test.local
hostnamectl

# DNS server
cat /etc/resolv.conf

# hosts file
vi /etc/hosts
```
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
```
# with umask
# The first digit of the umask represents special permissions (sticky bit, ). The last three digits of the umask represent the
# permissions that are removed from the user owner (u), group owner (g), and others (o) respectively.

# for current session. to make permanent, update bashrc
umask -S <level><operation><permission>

# change value for root in /etc/login.defs

# with acl, can keep user and group permissions the same but allow access to a file
getfacl file-name
setfacl -m u:username:symbolic_value file-name
# setfacl -m u:andrew:rw- group-project
# setfacl -m u:susan:--- group-project

```
  
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
```
ls -Z
restorecon -R /path
```
- Manage SELinux port labels
```
semanage port -l
semanage port -a -t port_label -p tcp|udp PORTNUMBER
semanage port -d -t gopher_port_t -p tcp 71
```
- Use boolean settings to modify system SELinux settings
```
sudo semanage boolean --list | head
sudo semanage boolean --modify --on http_allow_homedirs
```
- Diagnose and address routine SELinux policy violations
```
dnf install setroubleshoot-server

sealert -a /var/log/audit/audit.log
ausearch -m <search terms>
audit2why -a
```

## Manage containers
```
# install podman
dfn install podman
```

- Find and retrieve container images from a remote registry
```
podman search <image>
podman pull <image_full_name>
```

- Inspect container images
- Perform container management using commands such as podman and skopeo
```
podman logs <container_name_or_id>

# look at processes in pod
podman top <container_name_or_id>

# remove container
podman rm <container_name_or_id>
```
- Build a container from a Containerfile
```


```
  
- Perform basic container management such as running, starting, stopping, and listing running containers
```
# run container
podman run <options> <image_name>

# stop container
podman stop <container_name_or_id>

# start container
podman start <container_name_or_id>

# list running containers
podman ps
podman ps -a # shows stopped containers
```
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

