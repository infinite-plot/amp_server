**OPTIONAL STEP**

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
mkdir /mnt/YOURSHAREDFOLDERNAMEHERE # input your shared folder name
```
* mount the nfs share
```
sudo mount -t nfs YOURNASIPADDRESS:/volume1/YOURSHAREDFOLDERNAMEHERE /mnt/YOURSHAREDFOLDERNAMEHERE # note, this only mounts temporarily
```
* To mount the NAS at startup
```
sudo nano /etc/fstab # this is the file we need to update to get the NAS to mount at startup
```
* Enter this into the fstab file, save and exit
```
YOURNASIPADDRESS:/volume1/YOURSHAREDFOLDERNAMEHERE /mnt/YOURSHAREDFOLDERNAMEHERE nfs defaults,_netdev,nofail, 0 0 # these extra arguments help ensure the server still boots smoothly in the event the server can't access the NAS
```
* Reload mountpoints
```
sudo systemctl daemon-reload
sudo mount -a
```
* Check your mount point location to make sure all is good
