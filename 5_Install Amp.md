**Install AMP**
Run the following command as root by **using sudo su - first.** DO NOT put sudo in front of the command. DO NOT mistake the letter O for a number when typing the command.
```
sudo su - 
bash <(wget -qO- getamp.sh)
```
1. Terminal Configuration Wizard
    * System Password: You will be asked for a password for the new Linux system user (usually amp).
    * Panel Admin Credentials: Username: Choose a username for the web control panel (default is often admin). Password: Set a strong password for this administrative user.
    * Application Pre-requisites: Java: Select Yes if you plan to run Minecraft servers, as this installs the necessary Java runtimes.
    * SteamCMD: Select Yes if you intend to host games like Rust, Ark, or CS:GO to install required 32-bit libraries.
    * Docker Isolation: Choose whether to run game instances inside Docker containers for better security and isolation (recommended unless you have specific performance concerns).
    * HTTPS Setup: If you have a domain name pointed at the server, you can choose to configure SSL/TLS certificates via Let's Encrypt automatically. 
2. Web-Based Initial Setup
    * Once the terminal script finishes, it will provide a URL (usually http://your-server-ip:8080). 
    * Login: Access the URL in your browser and log in with the Panel Admin Credentials you created in Phase 1.
    * License Key: Enter your purchased AMP license key to activate the software. Purchase one if you haven't. The basic license is enough to get started for most people.
    * Privacy Settings: Choose whether to opt-in for crash reports or browser analytics to help CubeCoders improve the software.
    * Instance Creation: From the main dashboard, you can now click Create Instance, select your game (e.g., Minecraft, Valheim), and click Update or Start to bring your first game server online.
3. OPTIONAL - Create the Restricted Role for additional users
    * Navigate to Configuration > Role Management.
    * Click Create Role and name it something like "Instance Operator"
    * Edit the permissions for this role:
    * ADS Level: Grant "Login" access so he can get into the panel.
    * Instance Level: Search for the AMPCORE or Application section. Enable Start Application, Stop Application, and Restart Application.
    * IMPORTANT: Keep all "Configuration," "File Management," and "User Management" permissions unchecked (greyed out) to prevent the user from changing settings or deleting files. 
4. OPTIONAL - Create additional User Account
    * Go to Configuration > User Management. Click Create User.
    * Enter a username and password
    * In the Roles dropdown, select the "Instance Operator" role you just created. 
5. OPTIONAL - Assign the User to Specific Instances
    * By default, a new user might not see any servers. You must explicitly tell AMP which instances he is allowed to touch.
    * In the main ADS dashboard, right-click the specific game instance (e.g., your Minecraft server) and select Manage.
    * Once inside that instance’s specific panel, go to Configuration > User Management.
    * Find the username and assign "Manage Instance" or "Access Instance" permissions for only that specific server. 
