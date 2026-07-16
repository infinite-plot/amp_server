1. Updating AMP

Run the following command as root by **using sudo su - first.** DO NOT put sudo in front of the command. DO NOT mistake the letter O for a number when typing the command.
```
sudo su -l
getamp update
```


2. Updating Playit

You installed Playit via docker. So go to your the folder with Playit's docker-compose.yml file and then type:
```
sudo docker compose pull
sudo docker compose up
```


3. Updating cloudflared
 
You installed cloudflared via docker. So go to your the folder with cloudflared's docker-compose.yml file and then type:
```
sudo docker compose pull
sudo docker compose up
```


4. Updating Glances

You installed Glances via docker. So go to your the folder with Glances' docker-compose.yml file and then type:
```
sudo docker compose pull
sudo docker compose up
```


5. Updating Portainer
 
You installed Portainer via docker. So go to your the folder with Portainer's docker-compose.yml file and then type:
```
sudo docker compose pull
sudo docker compose up
```

