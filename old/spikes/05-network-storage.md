
zpool list

# Basics
An intro to network storage consists of sharing a publicly accesable read only iso NFS share.

sudo apt-get install -y zfsutils-linux
sudo modprobe zfs
sudo apt-get install -y nfs-kernel-server

sudo ufw allow from 192.168.1.66 to any port 2049 && \
sudo ufw allow from 192.168.1.69 to any port 2049 && \
sudo ufw allow from 192.168.1.66 to any port 111 && \
sudo ufw allow from 192.168.1.69 to any port 111 && \
sudo ufw allow from 192.168.1.66 to any port 4045 && \
sudo ufw allow from 192.168.1.69 to any port 4045 && \
sudo ufw reload


sudo ufw allow from 192.168.1.66 to any app Samba

# ZFS Host
If this system will use NFS (locking):
```
zfs create \
-o com.sun:auto-snapshot=false \
-o mountpoint=/var/lib/nfs \
rpool/var/nfs
```
sudo zfs set sharenfs="rw=@10.10.11.0/24" tank



sudo zpool create -f rpool /storage/isos

zfs create \
-o mountpoint=/ocean \
raidp/ocean

zfs create \
-o mountpoint=/ocean/iso \
raidp/ocean/iso

zfs create \
-o mountpoint=/ocean/vm \
raidp/ocean/vm

zfs list -o name,sharenfs,mountpoint
sudo chown -R ncrmro:ncrmro /ocean


## VM Iso's drive

sudo zfs set \
sharenfs="rw=@192.168.1.66/24,rw=@192.168.1.69/24" \
ocean/isos

sudo zfs set \
sharenfs="rw=@192.168.1.66/24,rw=@192.168.1.69/24" \
ocean/vms


sudo zfs get sharenfs raidp/ocean/vm

zfs list
zfs share raidp/ocean/iso


### Shot mounted shares
showmount -e
````
Export list for earthdiver:
/ocean/iso 192.168.11.0/24
````

## From client

sudo apt install nfs-common



## On [Mac]hine..
`sudo mkdir /private/ocean/ ; sudo mkdir /private/ocean/iso`

sudo mount -t nfs 192.168.1.169:/ocean/vm /ocean/vm

sudo mount -t nfs 192.168.1.169:/ocean/iso /ocean/iso

### Linux
sudo mount -t nfs 192.168.1.169:/ocean/iso /ocean/iso


sudo mkdir /private/ocean



sudo umount -l /ocean/iso
sudo umount -l /ocean/vm


https://www.cyberciti.biz/faq/apple-mac-osx-nfs-mount-command-tutorial/
https://github.com/zfsonlinux/zfs/issues/7692
https://linuxconfig.org/how-to-configure-a-nfs-file-server-on-ubuntu-18-04-bionic-beaver
https://www.hiroom2.com/2016/05/18/ubuntu-16-04-share-zfs-storage-via-nfs-smb/
https://serverfault.com/questions/377170/which-ports-do-i-need-to-open-in-the-firewall-to-use-nfs
https://askubuntu.com/questions/890981/how-to-configure-a-nfs-mounting-in-fstab


sudo nano /etc/fstab

192.168.1.169:/ocean/iso    /ocean/iso    nfs    defaults,proto=tcp,port=2049    0 0
192.168.1.169:/ocean/vm    /ocean/vm    nfs    defaults,proto=tcp,port=2049    0 0


## Samba

sudo apt-get install -y samba

sudo nano /etc/samba/smb.conf

sudo zfs set sharesmb=off ocean/timemachine

zfs create \
-o mountpoint=/ocean/timemachine \
ocean/timemachine


zfs create \
-o mountpoint=/ocean/guest \
ocean/guest


sudo zfs set sharesmb=on ocean/timemachine
sudo zfs set sharesmb=on ocean/guest



smbpasswd -a ncrmro


zfs get -r share.smb.all

sudo systemctl restart smbd

smbclient -L localhost

sudo chmod 777 /ocean/guest


https://linuxconfig.org/how-to-configure-samba-server-share-on-ubuntu-18-04-bionic-beaver-linux



Timemachine

sudo tmutil setdestination /Volumes/ocean_timemachine
diskutil cs convert disk4s2 -stdinpassphrase '!&Ple699*'


https://apple.stackexchange.com/questions/103800/how-can-i-enable-time-machine-encryption-on-the-command-line
https://forums.macrumors.com/threads/selecting-location-of-timemachine-backups.1544990/