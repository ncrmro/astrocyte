# NAS AKA - Earthdiver

# Hardware Requirements

At least 5 disks is recommended for raidz2.
In addition to five disks, a dedicated disk specifically an industrial SSD designed for high endurance 
is ideal as a Separate Intent Log (SLOG) for your ZFS Intent Log (ZIL) to be setup on.

# Manual Steps

Manual steps should be minimized eg once the device is active 
provisioning should be handed off and automated using ansible.

This guide was
[This](https://github.com/zfsonlinux/zfs/wiki/Ubuntu-18.04-Root-on-ZFS) guide was used as refrence.
It is a guide explaning how to install Ubuntu 18.04 Root on ZFS.
 
 
## Create Install Media
Boot the Ubuntu install media and select Try Linux when available. Then open a terminal once you land
in the desktop environment.

These commands are modified from the guide above and have been condensed as to reduce total steps.


## Remote access
Once your able to log into the baremetal go ahead and set up some basic remote access.
```
sudo apt-add-repository universe && \
sudo apt update && \
passwd && \
sudo apt --yes install openssh-server
```

## Initial Packages and Environment Variables
Once logged in remotly we can install some needed packages.
ssh in and
```
sudo -i
apt-add-repository universe && \
apt update && \
apt install --yes debootstrap gdisk zfs-initramfs
```

All commands ran regarding ZFS that need disk environment variables
should have the following command ran before proceeding, 
which should reflect your physical disks. 
 
```
export POOL=ocean && \
export DISK1=ata-ST2000DM006-... && \
export DISK2=ata-ST2000DM006-... && \
export DISK3=ata-ST2000DM006-... && \
export DISK4=ata-TOSHIBA_... && \
export DISK5=ata-WDC_WD20EARX-... && \
export SLOG_DISK1=...
```

## Prepare Physical Disks
First lets clear the partion table.
```
sgdisk --zap-all /dev/disk/by-id/$DISK1 && \
sgdisk --zap-all /dev/disk/by-id/$DISK2 && \
sgdisk --zap-all /dev/disk/by-id/$DISK3 && \
sgdisk --zap-all /dev/disk/by-id/$DISK4 && \
sgdisk --zap-all /dev/disk/by-id/$DISK5 && \
sgdisk --zap-all /dev/disk/by-id/$SLOG_DISK1
```

Now partition the disk bootloader's.

```
sgdisk     -n3:1M:+512M -t3:EF00 /dev/disk/by-id/$DISK1 && \
sgdisk     -n3:1M:+512M -t3:EF00 /dev/disk/by-id/$DISK2 && \
sgdisk     -n3:1M:+512M -t3:EF00 /dev/disk/by-id/$DISK3 && \
sgdisk     -n3:1M:+512M -t3:EF00 /dev/disk/by-id/$DISK4 && \
sgdisk     -n3:1M:+512M -t3:EF00 /dev/disk/by-id/$DISK5 && \
sgdisk     -n3:1M:+512M -t3:EF00 /dev/disk/by-id/$SLOG_DISK1
```

Then partion the main partion.

```
sgdisk     -n3:1M:+512M -t3:EF00 /dev/disk/by-id/$DISK1 && \
sgdisk     -n3:1M:+512M -t3:EF00 /dev/disk/by-id/$DISK2 && \
sgdisk     -n3:1M:+512M -t3:EF00 /dev/disk/by-id/$DISK3 && \
sgdisk     -n3:1M:+512M -t3:EF00 /dev/disk/by-id/$DISK4 && \
sgdisk     -n3:1M:+512M -t3:EF00 /dev/disk/by-id/$DISK5 && \
sgdisk     -n3:1M:+512M -t3:EF00 /dev/disk/by-id/$SLOG_DISK1
```

## ZFS: Pool & Slog

We create the pool with following settings.

* ashift=12 is recommended here because many drives today have 4KiB (or larger) physical sectors
* normalization=formD eliminates some corner cases relating to UTF-8 filename normalization
* xattr=sa vastly improves the performance of extended attributes.
* Make sure to include the -part1 portion of the drive path. If you forget that, you are specifying the whole disk, which ZFS will then re-partition, and you will lose the bootloader partition(s).

```
zpool create \
-o ashift=12 \
-O atime=off 
-O canmount=off \ 
-O compression=lz4 \ 
-O normalization=formD \
-O xattr=sa \
$POOL raidz2 $DISK1 $DISK2 $DISK3 $DISK4 $DISK5
zfs set compression=lz4 $POOL
zfs set acltype=posixacl $POOL
```
  
ZFS: SLOG
```
zpool add $POOL log $SLOG_DISK1 
```

## System Instillation
```
zfs create \
-o canmount=off \
-o mountpoint=none \
$POOL/ROOT
```

Make the Root Dataset
```
zfs create -o canmount=noauto -o mountpoint=/ $POOL/ROOT/ubuntu && \
zfs mount $POOL/ROOT/ubuntu && \
zfs create -o setuid=off $POOL/home && \
zfs create -o mountpoint=/root $POOL/home/root && \
zfs create -o canmount=off -o setuid=off -o exec=off $POOL/var && \
zfs create -o com.sun:auto-snapshot=false $POOL/var/cache && \
zfs create -o acltype=posixacl -o xattr=sa $POOL/var/log && \
zfs create $POOL/var/spool && \
zfs create -o com.sun:auto-snapshot=false -o exec=on $POOL/var/tmp && \
zfs create -o com.sun:auto-snapshot=false -o mountpoint=/var/lib/nfs $POOL/var/nfs && \
zfs create -o com.sun:auto-snapshot=false -o setuid=off $POOL/tmp && \
chmod 1777 /mnt/tmp
```

Install the minimal system:
```
chmod 1777 /mnt/var/tmp && \
debootstrap bionic /mnt && \
zfs set devices=off $POOL
```

Hostname
```
echo HOSTNAME > /mnt/etc/hostname
# vi /mnt/etc/hosts
Add a line:
127.0.1.1       HOSTNAME
or if the system has a real name in DNS:
127.0.1.1       FQDN HOSTNAME
```

Namework Interface

```
Find the interface name:
# ip addr show

# vi /mnt/etc/netplan/01-netcfg.yaml
network:
  version: 2
  ethernets:
    enp3s0:
      dhcp4: true
    enp4s0:
      dhcp4: true 
```

Configure the package sources
```
# nano /mnt/etc/apt/sources.list
deb http://archive.ubuntu.com/ubuntu bionic main universe
deb-src http://archive.ubuntu.com/ubuntu bionic main universe

deb http://security.ubuntu.com/ubuntu bionic-security main universe
deb-src http://security.ubuntu.com/ubuntu bionic-security main universe

deb http://archive.ubuntu.com/ubuntu bionic-updates main universe
deb-src http://archive.ubuntu.com/ubuntu bionic-updates main universe
```
Bind the virtual filesystems from the LiveCD environment to the new system and chroot into it:

```
mount --rbind /dev  /mnt/dev && \
mount --rbind /proc /mnt/proc && \
mount --rbind /sys  /mnt/sys && \
chroot /mnt /bin/bash --login
```

Configure a basic system environment, languages, and packages.
```
ln -s /proc/self/mounts /etc/mtab && \
apt update && \
apt install --yes locales && \
dpkg-reconfigure locales && \
dpkg-reconfigure tzdata && \
apt install --yes nano && \
apt install --yes --no-install-recommends linux-image-generic && \
apt install --yes zfs-initramfs && \
apt install dosfstools
```

Install GRUB for UEFI booting, system groups and set a root password.
```
mkdosfs -F 32 -n EFI /dev/disk/by-id/$DISK1-part3 && \
mkdosfs -F 32 -n EFI /dev/disk/by-id/$DISK2-part3 && \
mkdosfs -F 32 -n EFI /dev/disk/by-id/$DISK3-part3 && \
mkdosfs -F 32 -n EFI /dev/disk/by-id/$DISK4-part3 && \
mkdosfs -F 32 -n EFI /dev/disk/by-id/$DISK5-part3 && \
mkdosfs -F 32 -n EFI /dev/disk/by-id/$SLOG_DISK1-part3 && \
mkdir /boot/efi && \
echo PARTUUID=$(blkid -s PARTUUID -o value /dev/disk/by-id/$DISK1-part3) /boot/efi vfat nofail,x-systemd.device-timeout=1 0 1 >> /etc/fstab mount /boot/efi
echo PARTUUID=$(blkid -s PARTUUID -o value /dev/disk/by-id/$DISK2-part3) /boot/efi vfat nofail,x-systemd.device-timeout=1 0 1 >> /etc/fstab mount /boot/efi
echo PARTUUID=$(blkid -s PARTUUID -o value /dev/disk/by-id/$DISK3-part3) /boot/efi vfat nofail,x-systemd.device-timeout=1 0 1 >> /etc/fstab mount /boot/efi
echo PARTUUID=$(blkid -s PARTUUID -o value /dev/disk/by-id/$DISK4-part3) /boot/efi vfat nofail,x-systemd.device-timeout=1 0 1 >> /etc/fstab mount /boot/efi
echo PARTUUID=$(blkid -s PARTUUID -o value /dev/disk/by-id/$DISK5-part3) /boot/efi vfat nofail,x-systemd.device-timeout=1 0 1 >> /etc/fstab mount /boot/efi
echo PARTUUID=$(blkid -s PARTUUID -o value /dev/disk/by-id/$SLOG_DISK1-part3) /boot/efi vfat nofail,x-systemd.device-timeout=1 0 1 >> /etc/fstab mount /boot/efi
apt install --yes grub-efi-amd64 && \
passwd
```

Fix filesystem mount ordering

```
zfs set mountpoint=legacy $POOL/var/log
zfs set mountpoint=legacy $POOL/var/tmp
cat >> /etc/fstab << EOF
ocean/var/log /var/log zfs defaults 0 0
ocean/var/tmp /var/tmp zfs defaults 0 0
EOF

```

Grub instillation
```
update-initramfs -c -k all
```
```
# vi /etc/default/grub
Comment out: GRUB_HIDDEN_TIMEOUT=0
Remove quiet and splash from: GRUB_CMDLINE_LINUX_DEFAULT
Uncomment: GRUB_TERMINAL=console
Save and quit.
```
```
update-grub

grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=ocean --recheck --no-floppy


```

Initial Snapshot and first boot
`zfs snapshot $POOL/ROOT/ubuntu@install`
`exit`

`mount | grep -v zfs | tac | awk '/\/mnt/ {print $3}' | xargs -i{} umount -lf {}`
`zpool export $POOL`
`reboot`


Create a user account and add it to groups


```
zfs create $POOL/home/$POOL
adduser $USERNAME
cp -a /etc/skel/.[!.]* /home/$POOL
chown -R $POOL:$POOL /home/$POOL && \
usermod -a -G adm,cdrom,dip,plugdev,sudo $USERNAME
```

Bootloader UEFI 
`umount /boot/efi`

Swap

```
dd if=/dev/disk/by-id/$DISK1-part3 of=/dev/disk/by-id/$DISK2-part3 && \
dd if=/dev/disk/by-id/$DISK1-part3 of=/dev/disk/by-id/$DISK3-part3 && \
dd if=/dev/disk/by-id/$DISK1-part3 of=/dev/disk/by-id/$DISK4-part3 && \
dd if=/dev/disk/by-id/$DISK1-part3 of=/dev/disk/by-id/$DISK5-part3 && \
efibootmgr -c -g -d /dev/disk/by-id/$DISK2 -p 3 -L "ubuntu-2" -l '\EFI\Ubuntu\grubx64.efi' && \
efibootmgr -c -g -d /dev/disk/by-id/$DISK3-p 3 -L "ubuntu-3" -l '\EFI\Ubuntu\grubx64.efi' && \
efibootmgr -c -g -d /dev/disk/by-id/$DISK4 -p 3 -L "ubuntu-4" -l '\EFI\Ubuntu\grubx64.efi' && \
efibootmgr -c -g -d /dev/disk/by-id/$DISK5 -p 3 -L "ubuntu-5" -l '\EFI\Ubuntu\grubx64.efi' && \
mount /boot/efi
```

```
zfs create \
-V 4G -b $(getconf PAGESIZE) \
-o compression=zle \
-o logbias=throughput -o sync=always \
-o primarycache=metadata -o secondarycache=none \
-o com.sun:auto-snapshot=false $POOL/swap
```
```
mkswap -f /dev/zvol/$POOL/swap && \
echo /dev/zvol/$POOL/swap none swap defaults 0 0 >> /etc/fstab && \
echo RESUME=none > /etc/initramfs-tools/conf.d/resume
```

```
swapon -av
```

Full Software Installation
```apt dist-upgrade --yes  && apt install --yes ubuntu-standard```

```
# for file in /etc/logrotate.d/* ; do
    if grep -Eq "(^|[^#y])compress" "$file" ; then
        sed -i -r "s/(^|[^#y])(compress)/\1#\2/" "$file"
    fi
done
```

```
reboot
```

Final Cleanup


```
sudo zfs destroy rpool/ROOT/ubuntu@install && sudo usermod -p '*' root
```