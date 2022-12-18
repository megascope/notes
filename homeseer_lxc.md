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

## Setup USB device

Identify the controller USB device

```
lsusb | grep Home
# Bus 003 Device 002: ID 10c4:8a2a Silicon Labs HubZ Smart Home Controller
```

Note down the ID (in this case `10c4`:`8a2a`)

Edit UDEV rules to change the serial interface permissions

Edit or create `/etc/udev/rules.d/99-usb-serial.rules`. Add the following line
```
SUBSYSTEM=="tty", ATTRS{idVendor}=="10c4", ATTRS{idProduct}=="8a2a", SYMLINK+="zwave", OWNER="100000", GROUP="100005"
```

Where 
* `idVendor` and `idProduct` are the USB ID from above.
* `OWNER` is the sub id mapping of root in the container
* `GROUP` is the sub id mapping of the tty group in the container (i.e. `100000 + 5`)

Reload UDEV rules
```
udevadm control --reload
udevadm trigger
```

There should now be one (or two for a combo interface) ttyUSB files in dev, with a zwave symlink
```
ls -l /dev/zwave
# lrwxrwxrwx 1 root root 7 Dec 01 10:10 /dev/zwave -> ttyUSB1
```
You need to identify the actual interface(s) by looking at the symlink
```
ls -l /dev/ttyUSB*
# crw-rw---- 1 100000 100005 188, 0 Dec 01 10:10 /dev/ttyUSB0
# crw-rw---- 1 100000 100005 188, 1 Dec 01 10:10 /dev/ttyUSB1
```

Note down the group of the interface, in this case `188`


## Pass the USB controller into the container

Edit `/var/lib/lxc/homeseer/config` or equivalent


# References
* https://github.com/HomeSeerLinux/homeseerlinux.github.io#manual-installation
* https://deviant.engineer/2016/11/lxc-passthrough/
