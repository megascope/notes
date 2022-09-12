# Windows Internet Connection Sharing (ICS)

```shell
netsh wlan show drivers
netsh wlan set hostednetwork mode=allow ssid=<network name> key=<passkey>
netsh wlan start hostednetwork
```

If it says “hosted network couldn’t started”, you need to disable current wireless network device and then enable it. You may also need to refresh network adapter list from Device Manger to install a virtual network device driver.

When the hosted network is started, enable ICS for newly created Wi-Fi connection, so that you can share your internet connection with others.

```shell
netsh wlan stop hostednetwork
netsh wlan show hostednetwork
```
