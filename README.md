# Simple Plex Docker
A simple Plex/VPN/Sonarr/Radarr/Prowlarr/QBittorrent/Overseer config

This docker-compose will create 8 docker containers:
- Plex
- Overseer
- Glueton VPN (Compatible with 20+ providers) which routes external network traffic for:
    - Sonarr
    - Radarr
    - Prowlarr
    - QBittorrent
- (optional) Cloudflare tunnel for accessing Overseer remotely (requires cloudflare account and your own domain).

# Files to download:
- docker-compose.yml - contains the generic details and setup for the docker containers. You shouldnt need to edit anything here.
- config.yml - Config for all the containers. You will need to edit this to set directories, timezones, passwords, etc..

## Step 1: Download and set the config:
1. Download docker-compose.yml and config.yml to the directory you wish run your docker from.
2. Open the config.yml and read through the configuration details. Some variables you will need to change are:
   - docker_folder - the root directory where containers will create their config folders and files (created on first run).
   - media_folder - the root directory where your /movies, /tv, /downloads, /uncomplete directories are.
   - VPN Config: scroll down to the gluten vpn config and set the VPN config. This varies depending on your VPN provider. Full configuration details for providers are listed here: https://github.com/qdm12/gluetun-wiki/tree/main/setup/providers .
   - Cloudflare tunnel config: Login to cloudflare and create a tunnel, this will provide you with a tunnel key. Paste this over the cloudflare_token variable.

## Step 2: Running the server:
Navigate to the docker-compose.yml and run `sudo docker-compose up -d`

To stop the server, run `sudo docker-compose down` 

## Step 3: Initial configuration
Docker has now downloaded all the containers/software and switched them on, but they have not been configured to work together, folder locations, logins etc. You need to do the following intial set up. As some containers require API keys from other containers to talk to each other, it is reccommended to set them up in this order. You may need to replace [localhost] with your servers IP address if accessing these links from a different computer on your local network. Note that local SSL will not be valid for these services, so you'll need to allow exceptions in your browser to view these services.
1. Set up plex: Navigate to http://localhost:8000 and login to your plex server. Once logged in:
    - Under advanced settings, enforce TLS encryption
    - Set your movies and tv library folders to /movies and /tv
    - Copy the plex API key (used later on)
2. set up qbittorrent: Navigate to http://localhost:8000 and login to the qbittorrent web interface:
   - Set up the bittorrent/seeding configuration
   - Set up the uncomplete folder for downloading, and move to /completed folder when torrents are completed
   - Copy the web admin API key (used later on)
3. Set up prowlerr: Navigate to  http://localhost:8000 and login to the prowlerr server.
   - Create/add some trackers   
   - Copy the Prowller API key (used later on)
4. Set up Sonarr:  Navigate to http://localhost:8000 and login to the sonarr server.
   - Enter the proweller API key
   - Enter the plex API key
   - Enter the qbittorrent login details
   - Set the tv library folder
   - Set the quality restrictions and other configuration
   - Copy the Sonarr API key (used later on)
5. Set up Radarr: Navigate to http://localhost:8000 and login to the radarr server.
   - Enter the prowller API key
   - Enter the plex API key
   - Enter the qbittorrent login details
   - Set the movies library folder
   - Set the quality restrictions and other configuration
   - Copy the Radarr API key (used later on)
6. Set up Overseer: Navigate to http://localhost:8000 and login to the overseer server.
   - Enter the plex API key
   - Enter the sonarr API key
   - Enter the radarr API key
   - Set up the external domain
   - Disable plex account creation (we dont want anyone logging in)
   - Create local user accounts and request rules
7. Cloudflare tunnel: After running the server for the first time, the cloudflare tunnel dashboard should indicate connection success. On the cloudflare website, set you cloudflare tunnel to route traffic to http://localhost:8000. You should now be able to connect to overseer remotely through your tunnels subdomain.

## Step 4: Admin logins
After initial configurtion, you should keep the following URLs bookmarked. 
