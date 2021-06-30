# Home libre server

## Introduction

This guide is written from zero by Vitaly Bugaychuk. Only free and open source software is used.
The goal here is to move away from the cloud services, collecting data about you. Self-host all cloud services on your own spare machine.
I'm calling it server, but it could be any machine with 2 gigabytes of RAM or more.
Yes, you can run all this on a Raspberry Pi, with a few tweaks of course.
I'll try to make this as clear as possible, so even beginners can make use of this aweasomeness!
I assume you'll know how to configure port forwarding on your router to make this work.

We'll run the following services at home.
  * [PostgreSQL Database server](https://www.postgresql.org/) - we'll need this for nextcloud. 
  * [Nextcloud](https://nextcloud.com/) instance - Home/work cloud server, an alternative to O365, Google suite etc. Calendar and [WebDav](https://en.wikipedia.org/wiki/WebDAV) Ah, and almost forgot. Collaboration (audio/video/chat and collaboration on documents inside your web browser). Everything encrypted with TLS.
  * [Proxy](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/) - an Nginx reverce proxy, so we can run multiple services behind a single IP/domain.
  * [Letsencrypt-Companion](https://hiqdev.com/packages/docker-letsencrypt-nginx-proxy-companion/) - a bot that monitors all SSL/TLS cetfificates and updates them if needed.
  * [Jellyfin](https://jellyfin.org/) - media streaming service, so you can watch all your TV Series, movies on you TV and devices. As well as stream your music library. Just like Plex, although no account is needed, nor any information is collected about you. Just the way I like it.
  * [qBittorrent](https://www.qbittorrent.org/) - Torrent clint with a web GUI.
  * [PiHole](https://pi-hole.net/) - this is a DNS server for local use, that blocks most of the ads when browsing.
  * [Portainer](https://www.portainer.io/) - optionally if you like managing your containers from a web GUI. Personally I installed it, but used it one or two times in the last year.

## System requirements

I suggest to use a system with at least 2 cores CPU and minimum 4GB of ram (you can get off with less).
As of moment of writing all of the docker containers mentioned in the Intro are using 1.45GB of ram along with the Linux Debian OS that they're running in. Isn't this amazing?!
The CPU I'm using is Intel's 3rd gen i5.
Not the best, but it's small, cool and quiet!
The disks are up to you. Use drives as big as you like.
As an Operating System I'll be using Debian Buster base, but you can use whatever you desire.
I would recommend to purchase a domain, otherwise you can make one up and use pihole and add that as a DNS record to access services locally, I will not recommmend to use IP address instead it will not work with SSL. But if you have a VPN to your LAN then you will not need any domains at all.
We'll use example.com as our domain, change it to your own domain name.

## Few tips

I store all my container data along with the yaml file on a hard drive in a single directory for easier backup.
I also keep some data on the SSD inside the server, it's smaller in capacity, but load times are times faster than when the same data is placed on HDD.
This is not mandatory, but what I've observed during the deployment and testing that if you have your database on SSD load times dramatically decrease.
Basicly you can place everything (yaml, volumes and configurations) in a single folder on an HDD for easier backup.
Backuping the data is very important since, we're hosting our own cloud and the data secuirty is dependent on us and not someone else. Also we keep the ownership of our data!
I would stongly recommend to sync the data off-site in case of a catastrofic event.

## Let's start

1. I will assume that you already have Debian or Ubuntu server installed on your machine. I will not cover this here.

2. Make sure your system is up to date and you have installed [Docker](https://docs.docker.com/engine/install/debian/) and [docker-compose](https://docs.docker.com/compose/install/)

3. We define some variables on the HOST inside /etc/environment

TZ="Europe/Sofia"
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin"
PUID=1000
PGID=994
USERDIR="/srv/dev-disk-by-uuid-fbda1d19-be42-458f-95c0-835183fd73f6/"

  TZ must be your local [time-zone](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)
  PATH is the exec path for Debian buster, please reffer to your distro's bin folders if needed. You may already have this predefined.
  PUID is your user ID, or docker dedicated user
  PGID is your group ID, or docker dedicated group
  USERDIR is a path to your storage, make sure to keep a **backup** of the docker dir that we'll use as it will be used to store important data. This will be used a lot!
  
4. Clone this repo using git clone or download all the files in a zip with wget

5. Navigate to the dir and change the necessary entries in the db.env file as well as in the docker-compose.yml

6. Run the following command to start all the services
``docker-compose -f docker-compose.yml up``

7. Add DNS A-records to each service inside the webGUI of PiHole

8. Add DNS A-records to the puublic dns to your public IP address, this way you'll be able to access your services outside your LAN

9. Port forward your router from WAN port 80 to port 80 on your servers' internal IP address
   Port on your WAN 443 to the servers' IP on 443
   This will allow access to your reverse proxy which will have the SSL certificates and will secure all connections to nextcloud, jellyfin and others if you need.

10. Once you start all the services you'll be able to 
 * Access nextcloud from https://nextcloud.example.com and the rest of the services on their described ports.
 * Access Jellyfin and stream your movies and tv series from https://jellyfin.example.com
 * The let's encrypt bot will get the needed certificates and will auto-update them prior expiry.
 * You will be free of google's spying services
 * You will have your own media server and
 * DNS server that blocks ads 
 * Your own O365 with calling.
   Make sure to use your DNS, by adding a single entry of your server's IP address to your router's DHCP. 
   For example if your server address is 172.16.1.2 then your DHCP should handle this IP address as the DNS server for your LAN.
 * You can get a torrent with media file, once it's finished downloading it can be indexed and ready to waych in your Jellyfin, just like your own Netflix
   To achieve this add those same directories to the Jellyfin Library.

11. BACKUP your information on regular bases
  * I use rsyc for the purpose, but you can use any tool you feel comfortable working with.

This is version 1.01 of the guide and docker-compose.yaml. Updates will be applied after further testing this on a bare-bone server.
