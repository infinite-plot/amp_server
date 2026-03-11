**We will use synology here as an example for a NAS share. The advantages of a NAS are that we can use it for backups and to move files back and forth.**
1. Get ip address of game server
2. Create nfs permissions in synology
    * edit shared folder <YOURSHAREDFOLDERNAMEHERE>
    * NFS Permissions
    * Create permission
    * input IP address of game server
    * map all users to admin (or get stuck in permission hell)
    * check all three boxes at bottom for asynch, connections, subfolders
3. Install nfs package
```
sudo apt update
sudo apt install nfs-common
```
* make directory
```
mkdir /mnt/YOURSHAREDFOLDERNAMEHERE
```
* mount the nfs share
```
sudo mount -t nfs YOURNASIPADDRESS:/volume1/YOURSHAREDFOLDERNAMEHERE /mnt/YOURSHAREDFOLDERNAMEHERE
```
* to mount at startup
```
sudo nano /etc/fstab
```
* Enter this into the fstab file, save and exit
```
YOURNASIPADDRESS:/volume1/YOURSHAREDFOLDERNAMEHERE /mnt/YOURSHAREDFOLDERNAMEHERE nfs defaults,_netdev,nofail, 0 0
```
* Reload mountpoints
```
sudo systemctl daemon-reload
sudo mount -a
```
* Check your mount point location to make sure all is good
