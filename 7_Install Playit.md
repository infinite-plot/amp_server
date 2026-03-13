**Use docker to create playit as an agent within a docker container. This enhances security of the agent.**  
**Note, you'll need to go to playit.gg and copy your secret key before proceeding.**

1. Sign in to the Playit Dashboard
```
https://playit.gg/
```
2. Add a New Agent
In the dashboard, click Agents.
Click Add Agent.
Choose Linux / Docker as the installation method.
Playit will display a secret agent key.
Example:
```
playit-agent --secret abc123xyz456
```
2. Create your directory
```
mkdir /home/YOURUSERNAME/compose/playit # create a compose directory for playit
cd /home/YOURUSERNAME/compose/portainer # make sure you are in the portainer directory
```
3. Then, create a compose file:
```
sudo nano docker-compose.yml
```
4. Use compose file example below:
```
services:
  playit:
    image: playitcloud/playit:latest
    container_name: playit-agent
    restart: unless-stopped
    network_mode: host
    environment:
      - SECRET_KEY=your_secret_key_here  # Get this from playit.gg dashboard
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
```
5. Run playit with this command
```
sudo docker compose up -d
```
Once the container starts, the agent will automatically register with the Playit dashboard.
