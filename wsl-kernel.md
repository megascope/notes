# Build ZFS and USB support into WSL

## Install build deps
```
apt install -yqq build-essential autoconf automake libtool gawk alien fakeroot dkms libblkid-dev \
uuid-dev libudev-dev libssl-dev libncurses-devel \
zlib1g-dev libaio-dev libattr1-dev libelf-dev python3 \
python3-dev python3-setuptools python3-cffi libffi-dev flex bison
```

## Get sources
Download latest source release
* https://github.com/microsoft/WSL2-Linux-Kernel/releases
* https://zfsonlinux.org/

Extract into `zfs` and `kernel` folders

## Setup kernel

```
cd kernel

# setup kconfig
export KCONFIG_CONFIG=Microsoft/config-wsl

make menuconfig
# enable usb mass storage support in config-wsl CONFIG_USB_STORAGE=y
# device drivers -> USB Support -> USB Mass Storage = *
# and save file

# generate directories
make prepare
```

## Setup ZFS
```
cd ../zfs
sh autogen.sh

# setup kernel mods
./configure --prefix=/ --libdir=/lib --includedir=/usr/include --datarootdir=/usr/share --enable-linux-builtin=yes \
--with-linux=../kernel --with-linux-obj=../kernel

# copy mods
./copy-builtin ../kernel
```

## Add zfs support
```
cd ../kernel
echo "CONFIG_ZFS=y" >> $KCONFIG_CONFIG
```

## Build Kernel and Modules
```
make
make modules
```

## Copy image file to WINDOWS
```
mkdir /mnt/c/wslkernels
cp arch/x86/boot/bzImage /mnt/c/wslkernels/my_custom_kernel
```

## Create WSL kernel Config
_Note this is on the Windows host_
Edit `%USERHOME%\.wslconfig` to target new kernel, example (must include double slashes):
```
[wsl2]
kernel=C:\\wslkernels\\my_custom_kernel
```

Then shutdown existing WSL sessions and restart
```
wsl --shutdown
wsl --list -v # check if wsl session has been shut down
bash
```

## Copy modules
Back inside new WSL session with custom kernel, install module dependencies
```
cd kernel
sudo module_install
```


