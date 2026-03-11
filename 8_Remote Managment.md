Prerequisites:
* A Cloudflare account and an active domain added to your account. **If you don't have a domain, skip this step.**
* Docker installed and running on your Linux machine.

1. Log in to the Cloudflare Zero Trust Dashboard via your PC
2. Navigate to the Cloudflare Zero Trust Dashboard and go to Networks > Tunnels.
    * Create a New Tunnel
    * Click the Create a tunnel button.
    * Select Cloudflared as the connector type and click Next.
    * Enter a name for your tunnel (e.g., home-server) and click Save tunnel.
3. Obtain the tunnel token
    * On the next screen, select Docker as your environment installation method.
    * Cloudflare will display a specific docker run command with your unique tunnel token (e.g., docker run cloudflare/cloudflared:latest tunnel run --token <YOUR_TOKEN>).
    * Copy this token.
4. Compose the Docker Container for Cloudflared
    * Create a folder with a compose file as in the previous steps, this time for cloudflared
    * Enter the below into the compose file
```
services:
  cloudflared:
    image: cloudflare/cloudflared
    container_name: cloudflared
    environment:
      - TZ=Europe/Amsterdam # Change this to your timezone
      - TUNNEL_TOKEN=${TOKEN}
    restart: unless-stopped
    command: tunnel --no-autoupdate run
    networks:
      - cloudflared

networks:
  cloudflared:
    name: cloudflared
```
Make sure you're in the cloudflared directory you just created, then compose the docker file with:
```
sudo docker compose up -d
```
5. Verify the Connection
    * After running the command, return to the Cloudflare Zero Trust Dashboard. Your connector should appear as healthy and connected within a few seconds. Click Next to proceed to the configuration step.
    * Configure Public Hostnames (Optional but Recommended)
    * In the dashboard, you can define public hostnames to route traffic through your new tunnel to local services:
6. Go to the Public Hostnames tab.
    * Click Add a public hostname.
    * Specify a Subdomain (e.g., nas), select your Domain, define the Service Type (e.g., HTTP), and enter the internal URL of your service (e.g., http://localhost:5000).
    * Click Save hostname.
