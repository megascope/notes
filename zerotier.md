# ZeroTier on macOS

Stop the service
```
sudo launchctl unload /Library/LaunchDaemons/com.zerotier.one.plist
```
Start the service
```
sudo launchctl load /Library/LaunchDaemons/com.zerotier.one.plist
```
