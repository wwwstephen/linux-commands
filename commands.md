---ESSENTIAL COMMANDS---

documents and MAN

mandb (build db)
3 important man sections to know (1 commands, 5 config files, 8 admin commands (root))
man 5 fstab
 apropos -s 5 fstab (only show config files)
 man 5 exports (will only work after we install nfs)

files and directories

cp -a /home/bob/myfile.txt /home/bob/data/ (copy with attributes)
mv /home/bob/lfcs/* /home/bob/new-data/
ln -s /tmp /home/bob/link_to_tmp
ln /tmp /home/bob/link_to_tmp
stat somefile (exact modification time)

find and permissions

find /dev/ -min -5 (less than 5 minutes ago +5 more than 5 minutes ago)
chmod u+x,g=w,o-rwx file (remember ugo)
1 is execute, 2 is write, and 4 is read (octal)
7	read, write, and execute	4 + 2 + 1
 sudo find /var/log/ -perm -g=w ! -perm /o=rw > /home/bob/data.txt (-g=w exactly write bit (ignores other), /o=rw if it has either read or write)
 find /home/bob -size 213k -o -perm 402 > /home/bob/secfile.txt
chmod u+s,g+s,o+t /home/bob/datadir  (t applies to others, SETUID, SETGID, STICKY BIT T for others)
find /usr/share -name dogs.txt -type f
sudo find /var/ -type f -perm 777
sudo find /usr/ -type f -mmin -120
sudo find /usr/ -type f -size +5M -size -10M > /home/bob/size.txt
grep -rl "search_string" /path/to/folder (search a folder for a string and show only file name)

SED and grep

sed -i 's/enabled/disabled/g' /home/bob/values.conf
sed -i 's/disabled/enabled/gi' /home/bob/values.conf
sed -i '500,2000s/enabled/disabled/g' /home/bob/values.conf
sed -i 's~#%$2jh//238720//31223~$2//23872031223~g' /home/bob/data.txt
grep -E '[0-9]{5}' /home/bob/textfile > /home/bob/number (egrep)
grep -c '^2' /home/bob/textfile > /home/bob/count
^` anchors the match to the start of the line, `$` to the end. 
grep -ic '^section' /home/bob/testfile > /home/bob/count_lines
grep -w man /home/bob/testfile > /home/bob/man_filtered (whole word)
tail -n 500 /home/bob/textfile > /home/bob/last

SSL_certs

openssl req -newkey rsa:4096 -keyout priv.key -out cert.csr (an encrypted private key and CSR in one command)
openssl req -x509 -newkey rsa:4096 -noenc -days 365 -keyout priv.key -out kodekloud.crt (most efficient way to generate a self-signed certificate creates both the private key and certificate)
openssl x509 -in /home/bob/my.crt -noout -subject (read the common name 
**Big tip:** `man openssl-req` has an EXAMPLES section with almost exactly the commands in this file

git

git add *.cpp
git commit -m "Added C++ files"
git branch testing (or git checkout -b testing)
git branch -D testing 
git log --raw -1
git merge documentation
git config --global user.name "Bob"
git config --global user.email "bob@example.com"
git restore --staged main.cpp
git remote add origin https://github.com/bob/kode.git
git push -u origin master
git pull origin master

---OPERATIONS DEPLOYMENT---

boot scripts and services

sudo shutdown +120 (+ 120 seconds from now)
sudo shutdown -c (cancel)
sudo systemctl set-default graphical.target (boot into grapical UI)
#!/bin/bash (shebang!!!)
(Tar archieve script)
#!/bin/bash
tar czf /home/bob/archive.tar.gz -C /home/bob dir1
chmod u+x /home/bob/script.sh
/home/bob/script.sh
systemctl show --property MainPID --value ssh.service
systemctl is-enabled ssh.service
sudo systemctl status ssh.service > /home/bob/service.txt
sudo systemctl mask apache2.service (masking means it can't be started ever)
sudo systemctl unmask apache2.service

processes and logs

ps lax (shows process and NICE values)
/var/log/
pgrep -a sshd
sudo renice 9 <PID> (change nice value)
sudo lsof -p 1 > /home/bob/files.txt (list of files open by a process)
sudo journalctl -u ssh.service | grep Accepted | tail -1 (last accepted login)
pgrep rpcbind (find PID by name)
systemctl status ssh.service    # read the "Main PID:" line
sudo kill -HUP <PID> (send a signal)
sudo grep -r -a reboot /var/log/ (a makes grep treat binary files as text)
sudo journalctl -p err (only show logs of error priority)
sudo journalctl -p info -g '^c' (priority info and starts with letter c)

schedule tasks

CRON (repeat, exact time) Anacron (catch up, daily, weekly, monthly - will catch up if machine powered off) at (once)
crontab -e
crontab -l
crontab -r`
0 3 15 * * /usr/bin/touch test_passed (3am of the 15th every month)
sudo vi /etc/anacrontab (view/change anacron jobs)
sudo journalctl -u anacron  (check if anacron jobs have run)
sudo anacron -n -f (force anacron to rerun jobs)
atq (show all schedule at jobs)
atrm 3 4 5 (remove at jobss)
(schedule an AT job (one time))
sudo -i
at 15:30 August 20 2054
at> /usr/bin/touch atscheduler
at> <Ctrl+D>
0 0 1 * * /usr/bin/touch monthly (monthly cron job) (first of every month)
0 11 * * 7 /usr/bin/touch weekly (every sunday)
0 6,23 * * 0 sudo /usr/bin/systemctl restart nginx (every sunday at 6 and 11)

software packages and repos.

sudo apt update
sudo apt upgrade
apt search "apache http server"
sudo apt install apache2
dpkg --search ls (find out what package list belongs to)
apt manages the package repos, dpkg manages the actual package
dpkg --listfiles coreutils
sudo apt remove --autoremove ziptool
/etc/apt/sources.list (add an extra package)

system resources 

df -h / (disc free, human readable)
du -sh /bin/
free --mega
lscpu | grep 'per socket'
sudo xfs_repair -n /dev/vdd > /home/bob/fscheck 2>&1 (check xfs filesystem for errors)

kernal params

sysctl vm.swappiness                                        # read the live value
sudo sysctl -w vm.swappiness=10                             # change it now
echo "vm.swappiness=10" | sudo tee /etc/sysctl.d/swap.conf  # make it persistent
sudo sysctl --system                                        # apply all saved files now
sysctl -a | grep swap                                       # find a parameter's full name
sysctl does everything, so /proc/sys is mostly background
cat /proc/sys/vm/swappiness reads the same live value as sysctl vm.swappiness
/etc/sysctl.d/swap.conf  (where the values are read at boot, but not necessarily the live values)

SELinux
ls -Z, ps auxZ, id -Z (show labels)
getenforce (current enforcement mode) 
sudo setenforce 0
sudo chcon -t httpd_sys_content_t /var/www/html/test.html
sudo restorecon -R /var/log/ (restore to default)
ps auxZ | grep sshd
sudo semanage user -l
sudo semanage fcontext -a -t httpd_sys_content_t /var/www/html/test.htm (this is permanent -a for add a new rule)
sudo restorecon -v /var/www/html/test.htm

containers and VMs

virsh destroy TestMachine (turns it off)
virsh autostart VM1 (auto starts it at boot --disable to undo)
docker ps -a
docker pull docker.io/library/nginx
docker run -d -p 1234:80 --restart always --name website docker.io/library/nginx
docker ps -a                     # find containers using the image
docker stop website
docker rm website
docker rmi docker.io/library/nginx
virsh list --all
virsh start VM1
virsh destroy VM1
virsh undefine VM1
virsh setmaxmem VM2 80M --config
virsh setmem VM2 80M --config

environment profile

echo $MYVAR
/etc/environment (file for system-wide enviironment variables)
printenv
echo 'export MYVAR=TRUE' >> /home/bob/.bashrc
cp -r /etc/skel/. /home/bob/default_data/ (copy the new-user template)
sudo vi /etc/profile.d/welcome.sh (runs for every user at login)
sudo touch /etc/skel/README (give each new user a README)
PATH="$HOME/.config/bin:$PATH" (add this to .bashrc)

resource limits

/etc/security/limits.conf
nproc (number of processes)
ulimit -a (Show limits)
sudo visudo (change stuff with sudoers)
trinity ALL=(ALL) NOPASSWD: ALL (allows trinity to run any sudo command with no password)
stephen hard fsize 4096 (hard limit for file size)
@salesteam soft nproc 20 (@ is a group, soft is just a recommendation)


---USERS AND GROUPS---

sudo usermod -e 2030-03-01 jane
sudo useradd --system apachedev (create an account of style system, meant for services)
sudo usermod -e "" jane (never expire)
sudo useradd -m -s /bin/csh jack (m creates the home directory, -s is for the shell)
sudo userdel -r jack
sudo chage -d 0 jane (password changes on next login)
sudo usermod -aG developers jane (add group, no 'a' replaces all groups)
sudo groupadd -g 9875 cricket (specific GID)
sudo groupmod -n soccer cricket (change group name to cricket)
sudo useradd -u 5322 -G soccer sam (new user to group soccer)
sudo usermod -g rugby sam (change primary group)
sudo groupdel appdevs
sudo chage -W 2 jane (warn 2 days before expiry)

---NETWORKING---
networking and config services

ss tunlp (check ports)
/etc/hosts (hostname resolution)
ip addr show eth0 (find an interfaces IP address)
ip route show default (show default gateway)
ip addr add 192.168.9.3/24 dev eth1
sudo vi /etc/netplan/99-custom.yaml
sudo chmod 600 /etc/netplan/99-custom.yaml
sudo netplan apply
ip route show (show full routing table)
sudo vi /etc/systemd/resolved.conf DNS-8.8.8.8 (add a global DNS resolver)

packet filtering and ufw

sudo ufw allow 22 (Allows incoming connections on port 22 through the firewall)
sudo ufw enable
sudo ufc allow 53/tcp (allow the port for TCP only)
sudo ufw deny 443/tcp
sudo ufw delete deny 443/tcp
sudo ufw status numbered (this shows a table of the rules you have)
sudo ufw allow from 207.45.232.181 (you're allow FROM the IP, ports we're allowing TO the port)
sudo ufw allow from 10.11.12.0/24 (allow from a network range)
sudo ufw allow from 192.168.0.4 to any port 22 (to any local IP address, sudo ufw allow from 192.168.0.4 port 22 is the same)
sudo ufw delete <deny-rule-number>
sudo ufw insert <allow-rule-number> deny from 10.0.0.19

ssh and squid proxy

/etc/ssh/sshd_config
sudo systemctl restart ssh
sudo vi /etc/squid/squid.conf
sudo vi /etc/squid/squid.conf acl facebook dstdomain .facebook.com http_access deny facebook (block a website - he first matching rule takes precedence.)


---STORAGE---
Managing Partitions and Swap Space

lsblk
sudo mkswap /dev/vdb3
sudo swapon /dev/vdb3
swapon --show
sudo fdisk /dev/vdd (create partition - n for new partition, d for delete)
sudo swapoff /dev/vdd2
sudo cfdisk /dev/vdd (like fdisk but more user friendly - use it for resizing partitions.)

Creating filesystem and Mounting them at boot

findmnt /test (what type of mount is it (XFS, ext4, you can also do lsblk -f)
From empty disk to usable storage
sudo fdisk /dev/sda                 # 1. create a partition, e.g. /dev/sda1 (see file 20)
sudo mkfs.ext4 /dev/sda1            # 2. create a filesystem on it
sudo mkdir /data                    # 3. create a mount point (an empty directory)
sudo mount /dev/sda1 /data          # 4. mount it: the files on it now appear under /data
sudo vi /etc/fstab                  # 5. add a line so it's mounted again at every boot
sudo mount -a                       #    and check that the fstab line works
sudo umount /mnt (unmount)
vi /etc/fstab /dev/vde /test ext4 defaults 0 2 (mount at boot)
Use a device as swap at boot
/dev/vdd none swap defaults 0 0
sudo swapon -a              # turn on all swap in fstab now, without rebooting
swapon --show               # check: /dev/vdd should be listed
sudo xfs_admin -L "SwapFS" /dev/vdd

Filesystems and mount options

findmnt  /dev/sda1 (show a file systems mount options)
sudo mount -o ro,noexec,nosuid /dev/vdd1 /mnt (mount with specific options)
sudo mount -o remount,rw /dev/vdd1 /mnt (remount with different options)
/dev/vdd1 /mnt ext4 defaults,ro 0 2 (mount with options at boot)

NFS and network block devices

/etc/exports ( This file lists which directories the NFS server shares)
/home 10.0.0.0/24(ro) (share a directory RO to a specfic CIDR range)
sudo exportfs -r  (reload)
sudo exports -v (sudo exportfs -v lists every NFS share your Linux server is currently exporting, along with the options)
sudo mount 127.0.0.1:/home /mnt (mount - not at boot, no fstab)
127.0.0.1:/home /mnt nfs defaults 0 0 (mount at boot)
/home 192.0.0.0/24(ro) 127.0.0.10(rw,no_root_squash) (1 directory with different options per client)

LVM
The build order is always PV → VG → LV → filesystem → mount. Tearing down goes in reverse.
sudo apt install -y lvm2
sudo pvcreate /dev/vdd /dev/vde
sudo pvs
sudo pvremove /dev/vde
sudo vgcreate volume1 /dev/vdd
sudo vgextend volume1 /dev/vde
sudo vgreduce volume1 /dev/vde
sudo vgs (show a volume groups size)
sudo lvcreate --size 0.5G --name smalldata volume1
sudo lvresize --size 752M volume1/smalldata
sudo mkfs.xfs /dev/volume1/smalldata
sudo lvremove volume1/smalldata

RAID and ACL
cat /proc/mdstat (view raid status)
getfacl /home/bob/archive
setfacl --modify user:john:rw /home/bob/specialfile
setfacl --remove user:john /home/bob/specialfile
setfacl --modify group:mail:rx /home/bob/specialfile
setfacl --recursive --modify user:john:rwx /home/bob/collection/
sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/vdd /dev/vde (RAID level 1)