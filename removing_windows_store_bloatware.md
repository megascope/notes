# Removing Windows Store App Bloatware
A new Windows 10 install includes a lot of random store bloatware. This guide shows how to use powershell to remove the apps from both user accounts and from the machine in general. Note that creating a new user might result in these apps being reinstalled.


## Powershell commands
Start powershell as administrator

```
# List packages
Get-AppxPackage -AllUsers | Select Name

# remove packages for all users
Get-AppxPackage -AllUsers *bing* | Remove-AppxPackage
Get-AppxPackage -AllUsers *zune* | Remove-AppxPackage
Get-AppxPackage -AllUsers *office.sway* | Remove-AppxPackage
Get-AppxPackage -AllUsers *Spotify* | Remove-AppxPackage
Get-AppxPackage -AllUsers *king.* | Remove-AppxPackage
Get-AppxPackage -AllUsers *TheNewYorkTimes* | Remove-AppxPackage
Get-AppxPackage -AllUsers *Flipboard* | Remove-AppxPackage
Get-AppxPackage -AllUsers *Microsoft.MicrosoftSolitaireCollection* | Remove-AppxPackage
Get-AppxPackage -AllUsers *Flipboard* | Remove-AppxPackage

# remove packages from system
Get-AppXProvisionedPackage  -online | Format-List -Property PackageName
Get-AppXProvisionedPackage  -online | Where-Object {$_.PackageName -like "*3dbuilder*"} |  Remove-AppxProvisionedPackage -online
Get-AppXProvisionedPackage  -online | Where-Object {$_.PackageName -like "*bing*"} |  Remove-AppxProvisionedPackage -online
Get-AppXProvisionedPackage  -online | Where-Object {$_.PackageName -like "*zune*"} |  Remove-AppxProvisionedPackage -online
Get-AppXProvisionedPackage  -online | Where-Object {$_.PackageName -like "*GetStarted*"} |  Remove-AppxProvisionedPackage -online
Get-AppXProvisionedPackage  -online | Where-Object {$_.PackageName -like "*zune*"} |  Remove-AppxProvisionedPackage -online
```

