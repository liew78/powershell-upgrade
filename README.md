# Scripts
This repo contains the following script

## Upgrade-PowerShell.ps1
The `Upgrade-PowerShell.ps1` script is used to upgrade the installed version of
PowerShell on a Windows host to a newer version.

jborean did an excellent job with this script :D

This script can be run on the following hosts
* Windows Server 2008 (with SP2 installed)
* Windows Server 2008 R2 (with SP1 installed)
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows 7 (with SP1 installed)
* Windows 8.1
* Windows 10

When calling the script, the `-Version` parameter is used to specify the target
PowerShell version to install. The versions that can be set as the target are:
* 3.0
* 4.0
* 5.1 (default if not set)

All OS' can be upgraded to 5.1 except for Windows Server 2008. If running
on Powershell 1.0 then this script will first upgrade the version to 2.0
before running the checks. This is because a lot of the upgrade paths need
this version installed as a baseline. If the .NET Framework version
installed is less than 4.5.2, it will be upgraded to 4.5.2 as this is
supported on all hosts and is required for v5.0.

As multiple packages can be installed in this process, multiple reboots may
be required to continue with the install. If a reboot is required the
script will detect if the 'username' and 'password' parameters have been
supplied. Those credentials are needed for silent operation.

If they have been supplied it will automatically reboot and login
to continue the install process until it is all complete. If these
parameters are not set then it will just reboot and continue once the user
has logged back in manually before continuing.

A log of this process is created in
`$env:SystemDrive\temp\upgrade_powershell.log` which is usually `C:\temp\`.
This log can used to see how the script ran after an automatic reboot.

To run this script from any version of PowerShell, the following commands can
be run

```PowerShell
$url = "https://raw.githubusercontent.com/jborean93/ansible-windows/master/scripts/Upgrade-PowerShell.ps1"
$file = "$env:SystemDrive\temp\Upgrade-PowerShell.ps1"
$username = "Administrator"
$password = "Password"

(New-Object -TypeName System.Net.WebClient).DownloadFile($url, $file)
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force

# version can be 3.0, 4.0 or 5.1
&$file -Version 5.1 -Username $username -Password $password -Verbose
```

When setting `username` or `password`, these values are stored in plaintext in
the registry until the script is complete. Be sure to run the script below to
clear them out.

```PowerShell
# this isn't needed but is a good security practice to complete
Set-ExecutionPolicy -ExecutionPolicy Restricted -Force

$reg_winlogon_path = "HKLM:\Software\Microsoft\Windows NT\CurrentVersion\Winlogon"
Set-ItemProperty -Path $reg_winlogon_path -Name AutoAdminLogon -Value 0
Remove-ItemProperty -Path $reg_winlogon_path -Name DefaultUserName -ErrorAction SilentlyContinue
Remove-ItemProperty -Path $reg_winlogon_path -Name DefaultPassword -ErrorAction SilentlyContinue
```
