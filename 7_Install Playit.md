**Use docker to create playit as an agent within a docker container. This enhances security of the agent.**  
**Note, you'll need to go to playit.gg and copy your secret key before proceeding.**
1. Create a directory for playit as in the previous steps. Then, create a compose file:
```
sudo nano docker-compose.yml
```
See compose file example below.
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
2. Run playit with this command
```
sudo docker compose up -d
```
3. After installation, follow the instructions to setup an agent in the playit.gg dashboard. 
