# Homeseer on LXC

This will set up [Homeseer HS4](https://homeseer.com) running on an unprivileged LXC container, passing through the USB interface for a combined Z-Wave / Zigbee device `LNHUSBZB1`.


# Container setup

## Create the container

Create an unprivileged LXC container
* Assumes Sub UUID/GUID mapping is `100000`
* Name of container is `homeseer`
* Should be based on latest Ubuntu LTS release (not Debian, as this is missing key packages)

Start and attach to the container
```
lxc-start homeseer
lxc-attach homeseer
```
The following commands are inside the container.
Create the homeseer user and add group membership for `tty`
```
adduser homeseer
usermod -a -G tty homeseer
```
Note down the group id for group `tty`
```
getent group tty
# tty:x:5:homeseer
```
In this case the group id is `5`

Now stop the container on the host
```
lxc-stop homeseer
```

## Setup USB device

Identify the controller USB device

```
lsusb | grep Home
# Bus 003 Device 002: ID 10c4:8a2a Silicon Labs HubZ Smart Home Controller
```

Note down the ID (in this case `10c4`:`8a2a`)

Edit UDEV rules to change the serial interface permissions

Edit or create `/etc/udev/rules.d/99-usb-serial.rules`. Add the following lines
```
# for DUAL interface USB device
SUBSYSTEMS=="usb", ENV{ID_USB_INTERFACE_NUM}="$attr{bInterfaceNumber}"
SUBSYSTEM=="tty", ATTRS{idVendor}=="10c4", ATTRS{idProduct}=="8a2a", ENV{ID_USB_INTERFACE_NUM}=="00", SYMLINK+="zwave0", OWNER="100000", GROUP="100005"
SUBSYSTEM=="tty", ATTRS{idVendor}=="10c4", ATTRS{idProduct}=="8a2a", ENV{ID_USB_INTERFACE_NUM}=="01", SYMLINK+="zwave1", OWNER="100000", GROUP="100005"
```



Where 
* `idVendor` and `idProduct` are the USB ID from above.
* `OWNER` is the sub id mapping of root in the container
* `GROUP` is the sub id mapping of the tty group in the container (i.e. `100000 + 5`)

Note that we have 3 lines because the controller is a dual-serial interface device. If there is only one port just a single line would be necessary:
```
# for SINGLE interface device
SUBSYSTEM=="tty", ATTRS{idVendor}=="10c4", ATTRS{idProduct}=="8a2a", SYMLINK+="zwave0", OWNER="100000", GROUP="100005"
```

Reload UDEV rules
```
udevadm control --reload
udevadm trigger
```

There should now be one (or two for a combo interface) ttyUSB files in dev, with a zwave symlink
```
ls -l /dev/zwave*
# lrwxrwxrwx 1 root root 7 Dec 01 10:10 /dev/zwave0 -> ttyUSB0
# lrwxrwxrwx 1 root root 7 Dec 01 10:10 /dev/zwave1 -> ttyUSB1
```
You need to identify the actual interface(s) by looking at the symlink
```
ls -l /dev/ttyUSB*
# crw-rw---- 1 100000 100005 188, 0 Dec 01 10:10 /dev/ttyUSB0
# crw-rw---- 1 100000 100005 188, 1 Dec 01 10:10 /dev/ttyUSB1
```

Note down the Major ID of the interface, in this case `188`

## Pass the USB controller into the container

Edit `/var/lib/lxc/homeseer/config` or equivalent and add the follwing:

```
lxc.cgroup.devices.allow = c 188:* rwm
lxc.mount.entry = /dev/zwave0 dev/ttyUSB0 none bind,optional,create=file
lxc.mount.entry = /dev/zwave1 dev/ttyUSB1 none bind,optional,create=file
```

Start the container and verify that the serial devices are created correctly.

On the host
```
lxc-start homeseer
lxc-attach homeseer
```
In the container, if permissions and passthrough have been set up correctly the devices will appear with the right user and group (`root`/`tty`)
```
ls -l /dev/ttyUSB*
# crw-rw---- 1 root tty 188, 0 Dec 01 11:26 /dev/ttyUSB0
# crw-rw---- 1 root tty 188, 1 Dec 01 11:26 /dev/ttyUSB1
```

# Install Homeseer
The following commands should all be within the container

## Install dependencies
```
apt-get install --yes mono-complete mono-devel mono-vbnc flite chromium-browser aha ffmpeg alsa-base alsa-utils tmux curl wget nano
apt-get remove --yes brltty
```

## Install HomeSeer
Download Homeseer package from [official site](https://docs.homeseer.com/products/software/hs4-smart-home-software/hs4-release-notes-downloads) and install into `/opt/HomeSeer`. 

```
wget [url to tarball]
tar xzvf [tarball]
mkdir -p /opt
mv HomeSeer /opt
chown -R homeseer:homeseer /opt/HomeSeer
```

## Setup systemd
Create systemd unit file `/etc/systemd/system/homeseer.service` with contents
```
[Unit]
Description=HomeSeer Server
Documentation=https://homeseer.com
After=network-online.target remote-fs.target time-sync.target
Before=multi-user.target

[Service]
User=homeseer
WorkingDirectory=/opt/HomeSeer
ExecStart=/usr/bin/mono /opt/HomeSeer/HSConsole.exe --log
SyslogIdentifier=HS4
StandardOutput=null
StandardError=syslog
Restart=on-failure
RestartSec=30
KillMode=none
TimeoutStopSec=300
ExecStop=/opt/HomeSeer/hsstop.sh
AmbientCapabilities=CAP_NET_BIND_SERVICE

[Install]
WantedBy=multi-user.target
```

Finally enable and start the service

```
systemctl enable homeseer
systemctl start homeseer
```

The UI should now be available at `http://ADDRESS_OF_CONTAINER/`

# References
* [HomeSeerLinux](https://github.com/HomeSeerLinux/homeseerlinux.github.io)
* [LXC Passthrough](https://deviant.engineer/2016/11/lxc-passthrough/)
* [Homeseer Forums](https://forums.homeseer.com/forum/homeseer-products-services/system-software-controllers/hs4-hs4pro-software/1415987-installing-hs4-on-linux?view=thread)
