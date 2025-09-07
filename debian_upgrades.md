# Buster (10) to Bullseye (11)

```
# upgrade everything
apt update
apt full-upgrade
apt --purge autoremove

# change sources
sed -i 's/buster/bullseye/g' /etc/apt/sources.list
sed -i 's/buster/bullseye/g' /etc/apt/sources.list.d/*.list
sed -i 's#/debian-security bullseye/updates# bullseye-security#g' /etc/apt/sources.list

# install
apt update
apt full-upgrade
apt --purge autoremove
```

# Bullseye (11) to Bookworm (12)
```
# upgrade everything
apt update
apt full-upgrade
apt --purge autoremove

# change sources
sed -i 's/bullseye/bookworm/g' /etc/apt/sources.list
sed -i 's/bullseye/bookworm/g' /etc/apt/sources.list.d/*.list

# install
apt update
apt full-upgrade
apt --purge autoremove
```

# Bookworm (12) to Trixie (13)
From https://www.debian.org/releases/trixie/release-notes/upgrading.en.html

### Start from "pure" debian
```
# check for obsolete packages
apt list '?obsolete'
apt purge '?obsolete'


# remove dead config files
apt list '?config-files'
apt purge '?config-files'

# check failed/pinned/held packages
dpkg --audit
apt-mark showhold
```

### Configure packages
* you may need to add `contrib` and `non-free` to `debian.sources`.
```
# check and backup old sources list format
cp /etc/apt/sources.list /etc/apt/sources.list.bookworm

# check no bookworm sources left
grep bookworm /etc/apt/sources.list.d/* /etc/apt/*.list

# use the new deb822 format
cat > /etc/apt/sources.list.d/debian.sources << EOF
Types: deb
URIs: https://deb.debian.org/debian
Suites: trixie trixie-updates
Components: main non-free-firmware
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg

Types: deb
URIs: https://security.debian.org/debian-security
Suites: trixie-security
Components: main non-free-firmware
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg
EOF
```

### update carefully
```
# run the update
apt update

# two step upgrade
apt upgrade --without-new-pkgs
apt full-upgrade
apt --purge autoremove

```
