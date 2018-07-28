
zpool list


If this system will use NFS (locking):
```
zfs create \
-o com.sun:auto-snapshot=false \
-o mountpoint=/var/lib/nfs \
rpool/var/nfs
```
sudo zfs set sharenfs="rw=@192.168.11.0/24" tank



sudo zpool create -f rpool /storage/isos

zfs create \
-o mountpoint=/storage/isos \
raidp/isos

zfs list -o name,sharenfs,mountpoint

zfs set share=name=isos,path=/rpool/isos,prot=nfs



## Iso's drive
zfs set share=name=pp,path=/pub,prot=nfs,sec=sys,r=*,public rpool/public