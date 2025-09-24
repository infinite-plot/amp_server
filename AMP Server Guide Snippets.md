# AMP Server Guide

## Installing Debian
***You don't have to follow these exact instructions, especially if you know what you're doing. However, some steps later on might not work exactly the same if you choose to do something differently.***

- Select Language, Location and Keyboard
- Select the network interface for your wired ethernet connection
- Give your device a hostname, such as `amp-server` — if you don't know what to put in domain, you can leave it blank
- Don't provide a root password, just leave it blank and hit enter
- Enter a username and password. **DON'T FORGET IT**
- For partitions, use the entire disk and put all files in one partition.
- For the package manager, just use the defaults and leave proxy blank
- For software selection, deselect everything except for SSH server and the standard utilities

## Installing AMP:
*Either directly in the command line of your server or via SSH:*

First, install curl by running `sudo apt update && sudo apt install curl -y`

You need to run the install script as root:
```
sudo su -
bash <(curl -fsSL getamp.sh)
``` 

The script will then prompt you to create a username and password—this is used for logging into the AMP dashboard. 
Next, type `y` to choose running docker. For `Enable HTTPS`, type `n` unless you have a domain and want to use port forwarding and HTTPS. (That is not required for this tutorial)

After the script finishes running, you can navigate to the IP address and port that is shown on screen, but **be careful** not to accidentally press ctrl+c as it might exit the script. 

Login with the credential you just created, paste in your license key, and select "Standalone"  

## Installing Playit:
*Either directly in the command line of your server or via SSH:*

Follow the instructions at [playit.gg](https://playit.gg/download/linux). At the time of writing this, the install method is:
``` bash
curl -SsL https://playit-cloud.github.io/ppa/key.gpg | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/playit.gpg >/dev/null
echo "deb [signed-by=/etc/apt/trusted.gpg.d/playit.gpg] https://playit-cloud.github.io/ppa/data ./" | sudo tee /etc/apt/sources.list.d/playit-cloud.list
sudo apt update
sudo apt install playit
```

After it's installed, run `playit` and follow the instructions to setup an agent in the playit dashboard. 

### Running Playit as a Service
By default, the playit agent won't run if the system reboots or if the service crashes. To fix this, we can create a simple service file. 

Create a service file by running
``` bash
sudo nano /etc/systemd/system/playit.service
```

And then paste in the following:
``` ini
[Unit]
Description=Playit Service
After=network.target

[Service]
ExecStart=/usr/local/bin/playit start
Restart=always
RestartSec=5
WorkingDirectory=/home/YOURUSER
User=YOURUSER

[Install]
WantedBy=multi-user.target
``` 

Then we need to reload systemd, enable the service, and then start it. 
``` bash
sudo systemctl daemon-reload
sudo systemctl enable playit
sudo systemctl start playit

# Check the status to make sure everything looks okay
sudo systemctl status playit