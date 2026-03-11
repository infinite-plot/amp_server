**We need a variety of security layers here. We need to secure remote access, automate upgrades, and contain intrusion. 
While we're at it, we might as well install some monitoring services to make monitoring easier.**

1. Login to the machine using your username and password.  
2. Update your system:
```
sudo apt update && sudo apt full-upgrade -y
```
3. Create SSH keys. After completing this, you'll login using SSH and the key instead of a password. This is a lot more secure.
* Open windows powershell and generate SSH key
```
ssh-keygen -t ed25519
```
* Then specifiy path and name e.g. C:\Users\YOURUSERNAME\Downloads/sshkey
* add private key to ssh agent. Go to the Downloads directory in your Windows Powershell, and use the command ssh-add <private key filename>. In this case:
```
ssh-add sshkey
```
* open sshkey.pub with notepad, copy data
* log into your server pc via ssh, using command ssh YOURUSERNAME@YOURSERVERIPADDRESS. For example:
```
ssh user@192.168.1.25 # this is an example, you must enter the correct ip address for your server
```
* Note that you'll have to enter your password. We'll fix that in the next step.
* Add the ssh key to your authorized keys file.
```
sudo nano ~/.ssh/authorized_keys
```
* paste sshkey.pub data that you copied into a new line
* CTRL-X save and exit
* Now, logout and ssh back in. You'll notice that you no longer need to enter your password (as your windows device is now effectively your password).
* Once you confirm that this works, you can disable password login. If you have two PCs, it's probably best to repeat this step on a second PC as a backup for login purposes. NOTE: BE SURE THE CERTIFICATE WORKS OR YOU WILL HAVE TO REINSTALL THE OS.
```
sudo nano /etc/ssh/sshd_config
```
* Remove password authentication by setting "PasswordAuthentication no". Save and exit.
4. Protect against brute force attempts with fail2ban
```
sudo apt install fail2ban # limit login attempts
```
5. Log into your router, and set a static IP address for your server
6. Backups and Monitoring. Install Timeshift
```
sudo apt install timeshift # For system-level snapshots to revert if an update breaks something
```
7. Install Docker
* Update repositories
```
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/debian
Suites: $(. /etc/os-release && echo "$VERSION_CODENAME")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```
* Install docker. This is really important, as it boxes in the services into containers for security.
```
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo systemctl start docker
sudo systemctl enable docker
```
* Verify
```
sudo systemctl status docker
```
8. Install Portainer. Once Docker is installed and running, you can install Portainer to manage your Docker containers.
* Make a directory for your compose files
```
mkdir /home/YOURUSERNAME/compose # create compose directory for your compose files
```
* Make a directory for portainer
```
mkdir /home/YOURUSERNAME/compose/portainer # create portainer directory
```
* Create a compose file for portainer
```
sudo nano docker-compose.yml # create compose file
```
* Enter this into your compose file:
```
services:
  portainer:
    container_name: portainer
    image: portainer/portainer-ce:sts
    restart: always
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/data
    ports:
      - 9443:9443
      - 8000:8000 # Used for Edge Agents, can be removed if not needed
volumes:
  portainer_data:
    name: portainer_data
networks:
  default:
    name: portainer_network
```
* Run the portainer docker container
```
docker compose up -d # run the portainer container in detached mode
```
* Access Portainer Web UI. Open a web browser and navigate to: https://your-server-ip:9443
Note: Replace your-server-ip with actual IP Address
Set up the admin account by following the on-screen prompts.
9. Setup Glances: light weight at a glance monitoring of system performance
* Make a directory for glances
```
mkdir /home/YOURUSERNAME/compose/glances # create glances directory
```
* Create a compose file for glances
```
sudo nano docker-compose.yml # create compose file
```
* Enter and save this:
```
services:
  glances:
    image: nicolargo/glances:latest-full
    container_name: glances
    restart: always
    pid: host
    network_mode: host
    environment:
      - "GLANCES_OPT=-w"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - /etc/os-release:/etc/os-release:ro
```
* Run the glances docker container
```
docker compose up -d # run the glances container in detached mode
```
* Access Glances Web UI. Open a web browser and navigate to: https://your-server-ip:61208
10. Setup ufw firewall. We're going to allow the ports for the services we're going to use, and deny everything else.
```
sudo apt install -y ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow from 192.168.1.0/24 to any port 22    # SSH
sudo ufw allow from 192.168.1.0/24 to any port 8080  # AMP Web
sudo ufw allow from 192.168.1.0/24 to any port 9443  # Portainer
sudo ufw allow from 192.168.1.0/24 to any port 9090  # Cockpit
sudo ufw allow from 192.168.1.0/24 to any port 61208 # Glances
```
* If you're using a NAS 
```
sudo ufw allow from NASIPADDRESS to any port 2049 proto tcp
```
* Then enter this command:
```
sudo ufw --force enable
```
* The goal here is to lock down the NAS to within your internal network. But not to fear. With playit and cloudflared, we'll still be able to manage the server remotely and let friends onto our server.
