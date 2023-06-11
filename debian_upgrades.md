# Buster (10) to Bullseye (12)

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

# Bullseye (11) to Bookwork (12)
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
