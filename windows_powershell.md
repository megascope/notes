# Unlocking bitlocker

Powershell exposed over SSH, send password over stdin (from Linux) and use it to unlock a bitlocker drive:

```
cat password.txt | ssh HOSTNAME '$PlainTextPassword = [Console]::In.ReadToEnd().Trim();$SecureString = ConvertTo-SecureString -String $PlainTextPassword -AsPlainText -Force;Unlock-BitLocker -MountPoint "D:" -Password $SecureString'
```
