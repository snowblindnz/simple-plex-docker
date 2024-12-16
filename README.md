# simple-plex-docker
A simple Plex/VPN/Sonarr/Radarr/Prowlarr/QBittorrent/Overseer config

This docker-compose will create 8 docker containers:
- Plex
- Overseer
- Glueton VPN (Compatible with 20+ providors) which routes external network traffic for:
    - Sonarr
    - Radarr
    - Prowlarr
    - QBittorrent
- (optional) Cloudflare tunnel for accessing Overseer remotely (requires cloudflare account and your own domain).

# Files to download:
- docker-compose.yml - contains the generic details and setup for the docker containers. You shouldnt need to edit anything here.
- config.yml - Config for all the containers. You will need to edit this to set directories, timezones, passwords, etc..

## Step 1: Download and setting the config:
1. Download docker-compose.xml and config.xml to the directory you wish to run it from on your server.
2. Open the config.xml and search and replace the following variables
   - docker_folder - the root directory where containers will create their config folders and files (created on first run).
   - media_folder - the root directory where your /movies/tv/downloads directories are.
3. VPN Config: scroll down to the gluten vpn config and set the service: vpn_name / vpn_username / vpn_password / country config. This varies depending on your VPN providor.
4. Cloudflare tunnel config: Login to cloudflare and create a tunnel, this will provide you with a tunnel key. Paste this over the cloudflare_token variable.

## Step 2: Running the server:
Navigate to the docker-compose.yml and run
`sudo docker-compose up -d`
The server should now be running. Run `sudo docker-compose down` to stop the server.

## Step 3: Setting up the server:
1. Set up plex: Navigate to http://localhost:8000 and login to your plex server. Once logged in:
    - Under advanced settings, enforce TLS encryption
    - Set your movies and tv library folders to /movies and /tv
    - Copy your plex API key (used later on)
2. set up qbittorrent: Navigate to http://localhost:8000 and login to the qbittorrent web interface:
   - Set up the bittorrent configuration
   - Set up the web admin configuration (used later on)
3. Set up prowlerr: Navigate to  http://localhost:8000 and login to the prowlerr server.
   - Create/add some trackers   
   - copy the Prowller API key (used later on)
4. Set up Sonarr:  Navigate to http://localhost:8000 and login to the sonarr server.
   - enter the prowller API key
   - enter the plex API key
   - enter the qbittorrent login details
   - set the tv library folder
   - set the quality restrictions and other configuration
   - write down the sonarr API key (used later on)
5. Set up Radarr: Navigate to http://localhost:8000 and login to the radarr server.
   - enter the prowller API key
   - enter the plex API key
   - enter the qbittorrent login details
   - set the tv library folder
   - set the quality restrictions and other configuration
   - write down the radarr API key (used later on)
6. Set up Overseer: Navigate to http://localhost:8000 and login to the overseer server.
   - enter the plex API key
   - enter the sonarr API key
   - enter the radarr API key
   - set up the external domain
   - create user accounts and request rules
7. Cloudflare tunnel: After running the server for the first time, the cloudflare tunnel dashboard should indicate connection success. On the cloudflare website, set you cloudflare tunnel to route traffic to http://localhost:8000. You should now be able to connect to overseer remotely through your tunnels subdomain.
