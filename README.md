# Simple Plex Docker
A simple Plex/VPN/Sonarr/Radarr/Prowlarr/qBittorrent/Overseer config

This docker-compose will create 9 docker containers:
- Plex
- Overseer
- Glueton VPN (Compatible with 20+ providers) which routes external network traffic for:
    - Sonarr
    - Radarr
    - Prowlarr
    - qBittorrent
- Cloudflare tunnel which routes access to:
    - Overseer

The network structure for the containers is as follows:

![Network Structure](/network.png?raw=true "Optional Title")

- Plex traffic is routed directly to the internet. All Plex traffic is TLS encrypted. (Plex media server does not work well with VPNs).
- Overseer traffic is accessed via cloudlfare tunnel through an external subdomain (no issues with dynamic IPs/reverse Proxies).
- Torrent searching and downloading is secured via glueton forcing all external traffic for these containers to go through your VPN provider.

# Requirements:
- Server with docker installed
- Plex account
- VPN service that is compatible with glueton (https://github.com/qdm12/gluetun-wiki/tree/main/setup/providers)
- Free cloudflare account (Your own domain is reccomended. If you have no domain, overseer will be available via a random xxxxx.trycloudflare.com subdomain)

# Files to download:
- docker-compose.yml - contains the generic details and setup for the docker containers. You shouldnt need to edit anything here.
- config.yml - Config for all the containers. You will need to edit this to set directories, timezones, passwords, etc..

## Step 1: Set the config:
- Download docker-compose.yml and config.yml to the directory you wish run your docker from.
- Open the config.yml and read through the configuration details. Some things you will need to change are:   
   - Search and replace: docker_folder - the root directory where containers will create their config folders and files (created on first run, then loaded/updated whenever docker is running).
   - Search and replace: media_folder - the root directory where your /movies, /tv, /downloads, /uncomplete directories are. 
   - VPN config: scroll down to the gluten vpn config and set up your VPN. This varies depending on your VPN provider. Full configuration details for glueton supported VPNs are listed here: https://github.com/qdm12/gluetun-wiki/tree/main/setup/providers .
- Cloudflare tunnel config. To set this config you need to create a cloudflare tunnel:
   - Login to cloudflare and go to Zero Trust -> Networks -> Tunnels.
   - Create a tunnel of type 'cloudflared', go to the docker install. This will show you a docker command to run. Do NOT run this command.
   - Copy and paste the tunnel command into a text editor, and copy the token value.
   - Paste the token value over  'cloudflare_token' in the config.yml.
   - On the cloudflare page, click next and:
       - Set a subdomain and domain to host the tunel on.
       - For the service, select 'http', and enter 'localhost:5055'. This will route the tunnel through to Overseer on your local network.

## Step 2: Running the server:
### Linux:
Open the terminal and navigate to the docker-compose.yml folder and run `sudo docker-compose up -d`

To stop the server, run `sudo docker-compose down` 

### Windows users:
Use the docker software to start/stop the containers.

## Step 3: Initial configuration
Docker has now downloaded all the containers/software and switched them on, but they have not been configured to work together, folder locations, logins etc. You need to do the following intial set up. As some containers require API keys from other containers to talk to each other, it is reccommended to set them up in this order. You may need to replace [localhost] with your servers IP address if accessing these links from a different computer on your local network. Note that local SSL will not be valid for these services, so you'll need to allow exceptions in your browser to view these services.
1. Set up Plex: Navigate to http://localhost:32400/web and login to your Plex server. Once logged in:
    - Under advanced settings, enforce TLS encryption
    - Set your movies and tv library folders to /movies and /tv
    - Copy the Plex API key (used later on)
2. Set up qBittorrent: Navigate to http://localhost:8080 and login to the qBittorrent web interface:
   - Set up the bittorrent/seeding configuration
   - Set up the uncomplete folder for downloading, and move to /completed folder when torrents are completed
   - Copy the web admin API key (used later on)
3. Set up Prowlarr: Navigate to  http://localhost:9696 and login to the Prowlarr server.
   - Create/add some trackers   
   - Copy the Prowlarr API key (used later on)
4. Set up Sonarr:  Navigate to http://localhost:8989 and login to the Sonarr server.
   - Enter the Prowlarr API key
   - Enter the Plex API key
   - Enter the qBittorrent login details
   - Set the tv library folder
   - Set the quality restrictions and other configuration
   - Copy the Sonarr API key (used later on)
5. Set up Radarr: Navigate to http://localhost:7878 and login to the Radarr server.
   - Enter the Prowlarr API key
   - Enter the Plex API key
   - Enter the qBittorrent login details
   - Set the movies library folder
   - Set the quality restrictions and other configuration
   - Copy the Radarr API key (used later on)
6. Set up Overseer: Navigate to http://localhost:5055 and login to the overseer server.
   - Enter the Plex API key
   - Enter the Sonarr API key
   - Enter the Radarr API key
   - Set up the external domain
   - Disable Plex account creation (we dont want anyone logging in)
   - Create local user accounts and request rules
7. Cloudflare tunnel: After running the server for the first time, the cloudflare tunnel dashboard should indicate connection success. Overseer should be available over your tunnel via https://your.domain.com (no port required).

# Maintenance
It is strongly reccomended that you set up a seperate Watchtower container (https://containrrr.dev/watchtower/) which will monitor and auto-update all of your docker containers. This way your system will always be up to date.
