**If you don't have a domain, skip this step.**

**Question: Why put in all this work? What's the value we're getting?**  

**Answer: With these steps, you'll gain enterprise level protection from DDoS and brute forcing hacking attempts. Skip this, and you'll only be able to manage your services locally, or port forward and run the risks.**

Prerequisites:
* A Cloudflare account and an active domain added to your Cloudflare account. 
* Docker installed and running on your Linux machine.

1. Create a Zero Trust account. Go into the Cloudflare Zero Trust / Cloudflare One dashboard and make sure your account is initialized. Tunnel and Access live there.
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
6. Create a Protected Application
    * Go to the Public Hostnames tab. Click Add a public hostname.
    * Specify a Subdomain (e.g., glances), select your Domain, define the Service Type (e.g., HTTP), and enter the internal URL of your service (e.g., http://localhost:5000).
    * Example: Application name: glances, Domain: glances.example.com
    * You can this for all the services you want to remotely access, such as AMP and Glances to monitor your server remotely.
    * Click Save hostname.
**IMPORTANT: you must setup security Policies in ZeroTrust to control who accesses your services. DO NOT open them up to the whole world. Instead, leverage the Google identity service to force users to login using their google ID.**
8. Add Google as an Identity Provider. 
    * Go to Zero Trust Dashboard → Settings → Authentication → Login Methods
    * Click: Add new → Google
    * Cloudflare will prompt you for: Client ID, Client Secret
    * These come from Google Cloud Console.
9. Create Google OAuth Credentials
    * Open: https://console.cloud.google.com/
    * Select or create a project.
    * Enable Google Identity APIs - Go to: APIs & Services → Library
    * Enable: Google Identity Services
    * Create OAuth Credentials - Navigate to: APIs & Services → Credentials
    * Click: Create Credentials → OAuth Client ID
    * Application Type: Web Application
    * Configure Redirect URI: Cloudflare requires the redirect URI: https://<your-team>.cloudflareaccess.com/cdn-cgi/access/callback
    * Example: https://joe.cloudflareaccess.com/cdn-cgi/access/callback
    * Your team domain is visible in: Zero Trust → Settings → General → Team Domain
    * Save Credentials
    * Google will provide: Client ID, Client Secret
    * Return to Cloudflare and enter them.
10. Restrict emails to allowed Google Accounts
    * Open the Zero Trust Dashboard, then go to: Access → Applications
    * You should already have created the protected application (example: glances.example.com).
    * Click the application.
    * Add or Edit an Access Policy. Click: Add policy or edit an existing policy.
    * Policy structure looks like: Policy name, Decision, Include rules, Require rules, Exclude rules. For basic email restriction we only use Include.
    * Choose the “Emails” Rule. Under Include click: Add Rule
    * Select: Emails. You will see a field where you can enter allowed addresses.
    * Example: joe@gmail.com, admin@gmail.com, ops@gmail.com
    * Cloudflare will only allow login if the authenticated Google identity exactly matches one of these emails.
