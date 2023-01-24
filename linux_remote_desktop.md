# Set up Login Remote Desktop

A multiuser remote desktop that opens at the XDM login screen.

## Base

Start with Debian XFCE using GDM

## Enable XDMCP

One can use systemd socket activation in combination with XDMCP to automatically spawn VNC servers for each user who attempts to login, so there is no need to set up one server/port per user. This setup uses the display manager to authenticate users and login, so there is no need for VNC passwords. The downside is that users cannot leave a session running on the server and reconnect to it later.

To get this running, first set up XDMCP and make sure the display manager is running. Then create:

`/lib/systemd/system/xvnc.socket` ->

```
[Unit]
Description=XVNC Server

[Socket]
ListenStream=5900
Accept=yes

[Install]
WantedBy=sockets.target
```

`/etc/systemd/system/xvnc@.service` ->

```
[Unit]
Description=XVNC Per-Connection Daemon

[Service]
ExecStart=-/usr/bin/Xvnc -inetd -query localhost -localhost -geometry 1920x1080 -once -SecurityTypes=None
User=nobody
StandardInput=socket
StandardError=syslog
```

Link socket service into systemd:
```
ln -s /lib/systemd/system/xvnc@.service /etc/systemd/system/xvnc@.service 
```

Start/enable `xvnc.socket`:
```
systemctl enable xvnc.socket
systemctl start xvnc.socket
```

Now, any number of users can get unique desktops by connecting to port 5900.

If the VNC server is exposed to the internet, add the -localhost option to Xvnc in xvnc@.service (note that -query localhost and -localhost are different switches) and follow #Accessing vncserver via SSH tunnels. Since we only select a user after connecting, the VNC server runs as user nobody and uses Xvnc directly instead of the vncserver script, so any options in ~/.vnc are ignored. 

( See https://wiki.archlinux.org/title/TigerVNC "Running Xvnc with XDMCP for on demand sessions" )
