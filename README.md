# nginx-rtmp-server
nginx based RTMP server using nginx-rtmp-module 

# Synopsys
This is a basic RTMP streaming server that ingests RTMP streams and outputs video to a web server for Internet browser use within the local NAT layer. It utilizes nginx via the [nginx-rtmp-module](https://github.com/arut/nginx-rtmp-module) to host the RTMP server itself and to host the web server for the flash based viewing. It also dumps the stream to a file at the location “/home/gmstv/announcements”.

# Usage
Ingest Server URL(s)    @ *rtmp://localhost/live/test*  OR *localhost:1935/live/test*
Output Server URL(s)	  @ *http://localhost/player*     OR *http://localhost/player2*
Recording Save location @ */home/gmstv/announcements/*
