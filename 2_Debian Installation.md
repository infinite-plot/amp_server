**We're going with Debian in headless mode for the OS. It's well optimized to run headless with 500MB of RAM.**
1. Download Balena Etcher so you can write the Debian ISO image to a USB.  
2. Download the Debian amd64 ISO image from debian.org  
3. Write the Debian amd64 ISO image to the USB.  
4. Insert the USB into the PC and boot it up.
5. Select Language, Location and Keyboard.
6. Select the network interface for your wired ethernet connection.  
7. Give your device a hostname, such as amp-server — if you don't know what to put in domain, you can leave it blank.  
8. Leave root username and password blank.
9. Enter a username and password, save to your password manager, and make the password SECURE.  
10. For partitions, use the entire disk and put all files in one partition.  
11. For the package manager, just use the defaults and leave proxy blank.  
12. For software selection, deselect everything except for SSH server and the standard utilities.  
