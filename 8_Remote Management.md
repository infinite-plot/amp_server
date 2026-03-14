**If you don't have a domain, skip this step.**

**Question: Why put in all this work? What's the value we're getting?**  

**Answer: With these steps, you'll get secure, remote access without exposing home IP addresses, opening router ports, or managing complex firewall rules. Skip this, and you'll only be able to manage your management services (i.e. glances, AMP) locally, or port forward and run the risks. Be careful exposing portainer, however, unless you really need remote access to its features.**

Prerequisites:
* A Cloudflare account and an active domain added to your Cloudflare account. 
* Docker installed and running on your Linux machine.

1. Create a Zero Trust account. Go into the Cloudflare Zero Trust / Cloudflare One dashboard and make sure your account is initialized. Tunnel and Access live there.
2. Navigate to the Cloudflare Zero Trust Dashboard and go to Networks > Connectors > Cloudflare Tunnels.
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
```
mkdir /home/YOURUSERNAME/compose/cloudflared
cd /home/YOURUSERNAME/compose/cloudflared
sudo nano docker-compose.yml
```
* Enter the below into the compose file
```
services:
  cloudflared:
    image: cloudflare/cloudflared:latest
    container_name: cloudflared
    environment:
      - TUNNEL_TOKEN=${TOKEN}
    restart: unless-stopped
    command: tunnel --no-autoupdate run
    networks:
      - cloudflared

networks:
  cloudflared:
    name: cloudflared
```
To avoid placing the token directly in the compose file, create a .env file in the same directory. This makes it harder to accidentally expose your Cloudflared token.
```
sudo nano .env
```
In this .env file, place your cloudflared token:
```
TOKEN=<Your tunnel token>
```
Make sure you're in the cloudflared directory you just created, then compose the docker file with:
```
sudo docker compose up -d
```
5. Verify the Connection
    * After running the command, return to the Cloudflare Zero Trust Dashboard. Your connector should appear as healthy and connected within a few seconds. Click Next to proceed to the configuration step.
    * Configure Public Hostnames (Optional but Recommended)
    * In the dashboard, you can define public hostnames to route traffic through your new tunnel to local services:
6. Create a Published Application Route
    * Go to the Published Application Route tab within Network > Connectors > Tunnels. Click Add a published application route.
    * Specify a Subdomain (e.g., glances), select your Domain, define the Service Type (e.g., HTTP), and enter the internal URL of your service (e.g., http://<yourserverip>:61208).
    * Example: Application name: glances, Domain: glances.example.com
    * You can this for all the services you want to remotely access, such as AMP and Glances to monitor your server remotely.
    * Click Save.
**IMPORTANT: you must setup security Policies in ZeroTrust to control who accesses your services. DO NOT open them up to the whole world. Instead, leverage the Google identity service to force users to login using their google ID.**
7. Create Google OAuth Credentials
    * Open: https://console.cloud.google.com/
    * Select or create a project.
    * Enable Google Identity APIs - Go to: APIs & Services → Library
    * Enable: Google Identity Services
    * Create OAuth Credentials - Navigate to: APIs & Services → Credentials
    * Click: Create Credentials → OAuth Client ID
    * Application Type: Web Application
    * Name - give this any name you like to identify your OAuth 2.0 client, for instance YOURTEAMNAME Google OAuth 2.0 client
    * Save your team domain to Google's "Authorized JavaScript Origins" in URIs 1. You can get this team domain from Cloudflare. Your team domain is visible in: Zero Trust → Settings → General → Team Domain. For example: https://YOURTEAMNAME.cloudflareaccess.com
    * Configure Redirect URI: Cloudflare requires the redirect URI: https://YOURTEAMNAME.cloudflareaccess.com/cdn-cgi/access/callback
    * Google will provide: Client ID, Client Secret
    * Return to Cloudflare and enter the Client ID, Client Secret
8. Add Google as an Identity Provider in Cloudflare. 
    * Go to Cloudflare → Zero Trust Dashboard → Settings → Integrations → Identity Providers
    * Click: Add an identity provider → Google
    * Cloudflare will prompt you for: Client ID, Client Secret from previous step
9. Restrict emails to allowed Google Accounts
    * Open the Zero Trust Dashboard, then go to: Access → Applications
    * You should already have created the protected application (example: glances.example.com).
    * Click the application.
    * Add or Edit an Access Policy. Click: Add policy or edit an existing policy.
    * Policy structure looks like: Policy name, Decision, Include rules, Require rules, Exclude rules. For basic email restriction we only use Include.
    * Choose the “Emails” Rule. Under Include click: Add Rule
    * Select: Emails. You will see a field where you can enter allowed addresses.
    * Example: joe@gmail.com, admin@gmail.com, ops@gmail.com
    * Cloudflare will only allow login if the authenticated Google identity exactly matches one of these emails.
