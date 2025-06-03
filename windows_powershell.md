# Unlocking bitlocker

Powershell exposed over SSH, send password over stdin (from Linux) and use it to unlock a bitlocker drive:

```
cat password.txt | ssh HOSTNAME '$PlainTextPassword = [Console]::In.ReadToEnd().Trim();$SecureString = ConvertTo-SecureString -String $PlainTextPassword -AsPlainText -Force;Unlock-BitLocker -MountPoint "D:" -Password $SecureString'
```

# Volume Shadow Copy (VSS)


## Enabling
First enable with
1. Search for _Create a Restore Point_.
1. Enable _System Protection_ in the dialog.
1. Turn on _Protection Settings_ for needed drives.

Or via administrator powershell
```
Enable-ComputerRestore -Drive "D:\"
```

## Creating a shadow copy
`vssadmin create` is only available on Windows Server/Enterprise. On other versions

```
wmic shadowcopy call create Volume='D:\'
```

## Managing
Using an administrator command prompt


List volumes eligble for shadow copies
```
vssadmin list volumes
```

List all shadow copies
```
vssadmin list shadows [/for=c:\]
```

Remove a shadow copy
```
vssadmin delete shadows /for=d:\ /oldest
```


## Restoring
Windows Explorer: Right Click a folder, Select Previous Version, Open.
