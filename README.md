# nginx-rtmp-server
NGINX based RTMP server using nginx-rtmp-module 

## Synopsys
This is a basic RTMP streaming server that ingests RTMP streams and outputs video to a web server for Internet browser use within the local NAT layer. It utilizes NGINX with the [nginx-rtmp-module](https://github.com/arut/nginx-rtmp-module) to host the RTMP server itself and to host the web server for the flash based player. Streams are dumped to a file @ “/home/gmstv/announcements”. HLS is implimented to enable playback on Apple mobile devices.

A good guide for how to make a (basic) server like this can be found at the [OBS Forums](https://obsproject.com/forum/resources/how-to-set-up-your-own-private-rtmp-server-using-nginx.50/)

## Usage
RTMP Ingest URLs              @ *rtmp://localhost/live/test*  OR *localhost:1935/live/test*

HTTP Video Player Server URLs @ *http://localhost/player*     OR *http://localhost/player2*

Recording Save location       @ */home/gmstv/announcements/*

## Repo organisation
* NGINX Builds- Recent builds of NGINX for the server code
* usr/local/nginx       - NGINX webserver code