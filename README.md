# nginx-rtmp-server
NGINX based RTMP server using nginx-rtmp-module. _For full HD streaming, it is recomended to run the server off of a min 1Gbps capable NIC._ Debian is the only supported linux distro.

## Synopsys
This is a basic RTMP streaming server that ingests RTMP streams and outputs video to a web server for Internet browser use within the local NAT layer. It utilizes NGINX with the [nginx-rtmp-module](https://github.com/arut/nginx-rtmp-module) to host the RTMP server itself and to host the web server for the flash based player. Streams are dumped to a file @ “/home/gmstv/announcements”. HLS is implimented to enable playback on Apple mobile devices.

A good guide for how to make a (basic) server like this can be found at the [OBS Forums](https://obsproject.com/forum/resources/how-to-set-up-your-own-private-rtmp-server-using-nginx.50/)

## Usage
* RTMP Ingest URLs              @ *rtmp://localhost/live/test*  OR *localhost:1935/live/test*
* HTTP Video Player Server URLs @ *http://localhost/player*     OR *http://localhost/player2*
* Recording Save location       @ */home/gmstv/announcements/*

## Repo organisation
* NGINX Builds    - Mirror for recent builds of NGINX
* `usr/local/nginx` - NGINX webserver code
* `usr/local/nginx/html/bin-debug`  - Test RTMP Player exclusively for debug use
* `usr/local/nginx/html/player` - (Recomended) Stylised Player for end user
* `usr/local/nginx/html/player2` - Unstylised Player for end user

# Initial Setup Tutorial

## Install and Configuration

* Obtain and install from source the latest versions of both `nginx` and the `nginx-rtmp-module` following the instructions on the [nginx-rtmp-module wiki](https://github.com/arut/nginx-rtmp-module/wiki/Installing-on-Ubuntu-using-PPAs). (Debian is similar enough to Ubuntu, as it is based on the basic Debian framework)
* Git clone the contents of this repo to the default NGINX directory `/usr/local/nginx`

## Generating init Script and Starting Server

* Make file `/etc/init.d/nginx` with contents:

```bash
### BEGIN INIT INFO
# Provides:          nginx
# Required-Start:    $all
# Required-Stop:     $all
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: starts the nginx web server
# Description:       starts nginx using start-stop-daemon
### END INIT INFO

PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
DAEMON=/usr/local/sbin/nginx
NAME=nginx
DESC=nginx

test -x $DAEMON || exit 0

# Include nginx defaults if available
if [ -f /etc/default/nginx ] ; then
    . /etc/default/nginx
fi

set -e

. /lib/lsb/init-functions

case "$1" in
  start)
    echo -n "Starting $DESC: "
    start-stop-daemon --start --quiet --pidfile /usr/local/nginx/logs/$NAME.pid 
        --exec $DAEMON -- $DAEMON_OPTS || true
    echo "$NAME."
    ;;
  stop)
    echo -n "Stopping $DESC: "
    start-stop-daemon --stop --quiet --pidfile /usr/local/nginx/logs/$NAME.pid 
        --exec $DAEMON || true
    echo "$NAME."
    ;;
  restart|force-reload)
    echo -n "Restarting $DESC: "
    start-stop-daemon --stop --quiet --pidfile 
        /usr/local/nginx/logs/$NAME.pid --exec $DAEMON || true
    sleep 1
    start-stop-daemon --start --quiet --pidfile 
        /usr/local/nginx/logs/$NAME.pid --exec $DAEMON -- $DAEMON_OPTS || true
    echo "$NAME."
    ;;
  reload)
      echo -n "Reloading $DESC configuration: "
      start-stop-daemon --stop --signal HUP --quiet --pidfile /usr/local/nginx/logs/$NAME.pid 
          --exec $DAEMON || true
      echo "$NAME."
      ;;
  status)
      status_of_proc -p /usr/local/nginx/logs/$NAME.pid "$DAEMON" nginx && exit 0 || exit $?
      ;;
  *)
    N=/etc/init.d/$NAME
    echo "Usage: $N {start|stop|restart|reload|force-reload|status}" >&2
    exit 1
    ;;
esac

exit 0
```

* Give all execute privileges: 

`sudo chmod +x /etc/init.d/nginx`

* Add script to default runlevels: 

`sudo /usr/sbin/update-rc.d -f nginx defaults`

* Start server: 

`sudo /etc/init.d/nginx start` (Can be restarted by replacing `start` with `restart`, or `stop` to shutdown)

## Open RTMP Port via iptables

* Set `iptables` firewall rule opening the default RTMP port: (1935) 

`sudo iptables -A INPUT -p tcp --dport 1935 --jump ACCEPT; sudo iptables-save`

* Save current `iptables` config to file for later use: 

`iptables-save > /etc/firewall.conf`

* Make bash script `/etc/init.d/iptables-open-rtmp`:

```bash
#!/bin/bash
iptables-restore -n < /etc/firewall.conf
```

* Make `/etc/init.d/iptables-open-rtmp` executable: 

`sudo chmod 775 /etc/init.d/iptables-open-rtmp`

* Add `/etc/init.d/iptables-open-rtmp` to `rc2.d` so it will auto-execute upon reboot:

`sudo update-rc.d iptables-open-rtmp defaults`

Done! _Happy streaming, serving and tinkering!_