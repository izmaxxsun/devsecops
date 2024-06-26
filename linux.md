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
chmod 755 # read = 4, write = 2, execute =1

# change ownership
chown user1:group1 file1
chown --reference=<file_to_mimic> file1 # updates user/group based on another file

# set user or group ID, gives higher permission for a script (runs as owner or group)
sudo chmod u+s <file>
sudo chmod g+s <file
```

- Locate, read, and use system documentation including man, info, and files in /usr/share/doc
```
man <command>
info <command>
whatis <command>
whereis <command>

# locate files
locate <command>
sudo updatedb # updates index to find files

# info on rpm packages
sudo rpm -ql systemd
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
sudo reboot
sudo init 6

# Shutdown
sudo shutdown --halt +5
sudo init 0
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
# add rd.break parameter enforcing=0
# this puts system in emergency mode and you can do things like recover
mount -o remountmrw /sysroot
chroot /sysroot
passwd root
# for SELinux relabeling if you've made system changes
touch /.autorelabel
mount -o remount,ro /
restorecon /etc/shadow #restores context for SELinux
setenforce 1
getenforce
```

- Identify CPU/memory intensive processes and kill processes
```
# Instantaneous view of system, also has memory, cpu
# press shift+< or shift+> to sort
top

# Process details
ps -edf

# Kill process
## Get PID from top or ps
pidof <service>

# kill using one of the options below
kill [OPTION] PID
pkill <service>

## List of options
kill -l # all the options
kill -9 # forceful
kill -15 # graceful

## NICE values for process
## -20 is the least nice meaning it will take over
## +20 will let other processes run ahead of it
nice -n <nice_value> <process_to_run>

# update nice for running process with RENICE
renice +5 <pid>
```

- Adjust process scheduling
```
# Look at scheduling policies
chrt -m # view schedule options and priority range
chrt -p <pid> # view scheduling for process

# Adjust schedule
chrt -b -p 0 <pid>  # batch schedule
chrt -f -p 50 <pid>  # FIFO schedule
chrt -r -p 50 <pid>  # round robin
chrt -o -p 0 <pid>  # other

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
dnf install tuned

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
# /var/log/secure captures security events like ssh logins
# /var/log/boot.log shows issues during startup
# /var/log/audit.log has SELINUX logs

# analyze startup process
systemd-analyze
systemd-analyze blame

# Alternative is to use journalctl
journalctl -n <num_of_messages>
journalctl -p <priority_eg_crit>
journalctl -u <service_name>
journalctl -b # logs since last boot
journalctl --since=today
journalctl --since=21:20

# Storing journal after reboot
mkdir /var/log/journal
echo "SystemMaxUse=50M" >> /etc/systemd/journald.conf
systemctl restart systemd-journald
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
systemctl mask <service> # prevents service from running
systemctl restart <service>
```

- Securely transfer files between systems
```
# Secure copy uses SSH to transfer file, use same credentials
scp file1 user@192.268.1.3:/home/user
scp /etc/ssh/* user@<host>:/tmp # copies contents of directory to tmp folder

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
# list drives
df -h

# show unique IDs for disks
blkid

# List partitions
lsblk -a
fdisk -l

# Check for partition changes
partprobe

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
pvdisplay
```
- Assign physical volumes to volume groups
```
# Udemy lecture 190
# Create volume group
vgcreate <volume_group_name> <physical_volume_name>

# extend volume group
vgextend <volume_group> <partition_to_add> # extending with an LVM partition

# reduce volume group
vgreduce <volume_group> <partition_to_remove>

# remove volume group
vgremove <volume_group>

# Display volume group info
vgdisplay <volume_group_name>
```
- Create and delete logical volumes
```
# Udemy lecture 190
lvcreate -n <logical_volume_name> --size <size> <volume_group>

# Display logical volumes
lvs
lvdisplay

# extend logical volume
## check fdisk and physical volumes have room
lvextend -L+1G /dev/vg1/lv1  # extends LV1 by 1G

# reduce logical volume
lvresize -L-1G /dev/vg1/lv1

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
# create file system
mkfs.ext4 /dev/vg1/lv1

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

# Create logical volume for swap
lvcreate -n swap1 -L 2G <volume_group>

# Make swap
mkswap <name_of_swap> # e.g. /dev/vg1/swap1

# Enable swap
swapon <name_of_swap> # e.g. /dev/vg1/swap1

# check if swap is enabled
swapon -s

# Add to fstab
<swap_path>    swap    swap    defaults 0    0

# Mount
swapon -a
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

## Make sure User IDs the same on client and server
usermod -i <desired_user_id> <user>

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
# Check owner and group permissions, and change accordinagly
chmod <permissions> <file>

# Check ACL permissions
getfacl <file>

setfacl -m u:<user>:rwx  # sets permission for specific user
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
# Find a package
dnf search <package>
dnf list na* # finds any package starting with na

# List repositories on the system
dnf repolist all # this shows all including disabled repos
dnf repoinfo # details on currently used repos

# Info on a package
dnf info <package

# List packages
dnf list

# Install package
dnf install <package>
dnf localinstall <package>

# Remove package
dnf remove <package>

# See history
dnf history list

# Add remote repository
yum-config-manager --add-repo https://<remote_repository>

# Local repo
## Install util to create repo
dnf install createrepo

## Create directory
mkdir /root/local_repo

## Create repo
createrepo --database /root/local_repo

## Add to yum
yum-config-manager --add-repo file:///root/local_repo

vi /etc/yum.repos.d

# Working with module streams
dnf module list
dnf module install php:7.3/minimal # example of installing specific version
dnf module remove <package>
dnf module reset
```
- Modify the system bootloader
```
grub2-editenv list
grub2-set-default 1 # sets the kernel to 2nd option

# Modify config and then run make
vi /etc/default/grub
grub2-mkconfig -o /boot/grub2/grub.cfg
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
```
# network service
systemctl enable httpd  # create symlink to start on boot
systemctl disable httpd

# starting network connection automatically
nmcli connection modify eth0 connection.autoconnect yes
```
- Restrict network access using firewall-cmd/firewall
```
# list zones
firewall-cmd --get-zones
firewall-cmd --zone work --list-all

# create zone
firewall-cmd --new-zone servers --permanent

# add service
firewall-cmd --zone servers --add-service=ssh --permanent
firewall-cmd --zone servers --list-all

# add interface to zone
firewall-cmd --change-interface=enp0s8 --zone=servers --permanent

# set default zone
firewall-cmd --set-default-zone=servers

# available services
firewall-cmd --get-services

# reload
firewall-cmd --reload
```

## Manage users and groups
- Create, delete, and modify local user accounts
```
# Create local user
useradd user1
useradd -u 1010 -g 1005 user1 # specify uid and gid

# View user accounts
cat /etc/group | grep user1
cat /etc/passwd | grep user1

# add group
groupadd IT
cat /etc/group | grep IT

# add user to group
useradd -G IT user1

# delete user
userdel -r user1 # removes home dir as well

# modify user
usermod -l user1 user2 # rename user

# change password
passwd user1
```

- Change passwords and adjust password aging for local user accounts
```
# change local passwd
passwd

# change for user
passwd <username>

# Shows password policy for user
chage -l <username>

# Modify password policy
chage -M 30 user1 # expire in 30 days
chage -E 2025-01-01 user1

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

# delete group
groupdel <group>

# update group name
groupmod -n <new_name> <existing_name>

# Add user and assign group
useradd <username> -G <new_group>
cat /etc/passwd

# Change group for a user
usermod -a -G <group> <user>

# remove user from group
gpasswd -d user1 <group>

```
- Configure superuser access
```
# sudoers config file
visudo /etc/sudoers

# root user can run on any machine as any user, and run any command
root    ALL=(ALL)    ALL

# user2 can run on any host as root and run ls or df command
user2 ALL=(root)    /bin/ls, /bin/df
```

## Manage security
- Configure firewall settings using firewall-cmd/firewalld
```
# IP forwarding
vi /etc/sysctl.conf
net.ipv4.ip_forward=1


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
vi /etc/selinux/config

# view messages
vi /var/log/messages
```
- List and identify SELinux file and process context
```
# List
ls -Z

# view your permissions
id -Z

# View file info
stat <filename>

# view process
ps -auxZ
```
- Restore default file contexts
```
# list context
ls -Z

# change context
chcon unconfined_u:object_r:tmpt_t:s0 kafka.txt

# restore file
restorecon <file>

# restore directory
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
# get booleans
getsebool -a | grep virtualbox

# get boolean details
sudo semanage boolean --list | grep virtualbox

# set boolean
setsebool -P use_virtualbox on
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
```
podman inspect docker.io/library/httpd

# with skopeo can inspect without pulling image
dnf install skopeo
skopeo inspect docker://registry.fedoraproject.org/fedora:latest
```
  
- Perform container management using commands such as podman and skopeo
```
# run container in detached mode with specific port
podman run -dt -p 8080:80/tcp <image>

podman logs <container_name_or_id>

# look at processes in pod
podman top <container_name_or_id>

# remove container
podman rm <container_name_or_id>
```
- Build a container from a Containerfile
```
# example Containerfile
FROM registry.access.redhat.com/ubi8/ubi-init
RUN yum -y install httpd; yum clean all; systemctl enable httpd;
RUN echo "Successful web server test" > /var/www/html/index.html
RUN mkdir /etc/systemd/system/httpd.service.d/; echo -e "[Service]\nRestart=always" > /etc/systemd/system/httpd.service.d/httpd.conf
EXPOSE 80

# build
podman build -t mysysd . # or can point to containerfile

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
```
# using image built above
podman run -d --name=mysysd_run -p 80:80 mysysd

```

- Configure a container to start automatically as a systemd service
```
vim /etc/systemd/system/httpd-container.service

# contents of httpd-container.service
[Unit]
Description=httpd Container Service
Wants=syslog.service

[Service]
Restart=always
ExecStart=/usr/bin/podman start -a httpd-server
ExecStop=/usr/bin/podman stop -t 2 httpd-server

[Install]
WantedBy=multi-user.target
```

- Attach persistent storage to a container
```
mkdir -p /home/user/containers/disk1

podman run --privileged -it -v /home/user/containers/disk1:/mnt httpd /bin/bash
```

As with all Red Hat performance-based exams, configurations must persist after reboot without intervention.
