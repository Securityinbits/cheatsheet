# PowerShell Cheat Sheet for Blue Team

- [Basics](#basics)
- [Commands](#commands)
    - [Process](#process)
    - [Registry](#registry)
    - [File](#file)
        - [Gather file hashes](gather-file-hashes)
- [References](references)


## Basics
### Calculated properties using Hashtable
Calculated properties that require a Hashtable with a Name/label and an Expression key can be used with **Select-Object** . The name key is the property name and the Expression key is a scriptblock that will be executed as **Select-Object** receives input.
```powershell
@{ Name = ''; Expression = {}}
```
Using E/Expression we are calculating the MD5 & SHA256 of each file returned by Get-ChildItem as shown below in the [gather file hashes](#gather-file-hashes) section.

## Commands
I will recommend reading this [PowerShell Commands for Incident Response](https://www.securityinbits.com/incident-response/powershell-commands-for-incident-response/) article to get a better understanding of the commands discussed below. I have extracted the PowerShell Commands from the above article for quick reference.
**Make sure you are running the PowerShell with admin privilege otherwise some of the cmdlets will not work properly**

### Process
Get all process with standard column
```powershell
Get-Process
```

Get Id, ProcessName, Path, Company, StartTime with **Select-Object** cmdlet
```powershell
Get-Process ProcName | Select-Object Id, ProcessName, Path, Company, StartTime | Format-Table
```

**Get-Process** cmdlet doesn’t support the process command line so use **Get-WmiObject** command
```powershell
Get-WmiObject -Class Win32_Process -Filter "name='process.exe'" | Select-Object ProcessId, ProcessName, CommandLine
```

Terminate Process
```powershell
Get-Process ProcName | Stop-Process
```

### Registry
**Get-ItemProperty** cmdlet can be used for listing registry entries
```powershell
Get-ItemProperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Run' -Name 'IMAP Service'
```
**Remove-ItemProperty** can be used for removing persistence registry entries created by malware. This example is from NanoCore RAT.
```powershell
Get-ItemProperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Run' | Remove-ItemProperty -Name 'IMAP Service'
```
### File
We can **Get-ChildItem** cmdlet to list the directory it’s like dir cmd. This cmdlet can be used in file system directory, registry hive, or a certificate store.
-Recurse – Used to recursive list all the sub-dir
-Filter – You can use the parameter to filter the path and it supports * and ? wildcards e.g *.dat, *.exe
```powershell
Get-ChildItem -Path $Env:APPDATA -Force -Recurse -Filter run.dat
```
**-Force – It is used to list hidden or system files. Some malware use the hidden attribute for their files, so always use this parameter**

Instead of **Get-ChildItem**, we can Test-Path to check if the dir or file exists or not
```powershell
Test-Path -Path $Env:APPDATA\*\run.dat
```
Create new directory
```powershell
New-Item -ItemType Directory -Path C:\Users\admin\IoCs
```

Copy directory recursively
```powershell
Copy-Item C:\Users\admin\AppData\Roaming\0319B08F-2B65-4192-B2D2-1E2F62087064\ -Destination C:\Users\admin\IoCs\ -Recurse
```

Delete the complete dir recursively
```powershell
Remove-Item -Path $env:APPDATA\0319B08F-2B65-4192-B2D2-1E2F62087064\ -Recurse -Force
```

Remove the copy of the NanoCore malware
```powershell
Remove-Item -Path $env:TEMP\RAVBg64.exe -Force
```

#### Gather file hashes
**Get-FileHash** cmdlet can be used to get the hash using a different algorithm e.g. MD5. SHA1 , SHA256 etc. By default, the Get-FileHash cmdlet uses the SHA256 algorithm, although any hash algorithm that is supported by the target operating system can be used.

**SHA256**
```powershell
Get-FileHash -Path 'C:\Users\admin\AppData\Roaming\0319B08F-2B65-4192-B2D2-1E2F62087064\IMAP Service\imapsv.exe'
```

**MD5**
```powershell
Get-FileHash -Algorithm MD5 -Path 'C:\Users\admin\AppData\Roaming\0319B08F-2B65-4192-B2D2-1E2F62087064\IMAP Service\imapsv.exe'
```

Command for collecting the file hashes in the directory with MD5, SHA256, Name & FullName and exporting the result in the file using **Export-Csv**
Using E/Expression we are calculating the MD5 & SHA256 of each file returned by **Get-ChildItem**

```powershell
Get-ChildItem -Path C:\Users\admin\AppData\Roaming\0319B08F-2B65-4192-B2D2-1E2F62087064\ -Force -Recurse -File | Select-Object @{Name='MD5';E={(Get-FileHash -Algorithm MD5 $_).Hash}}, @{N='SHA256';E={(Get-FileHash -Algorithm SHA256 $_).Hash}},Name, FullName | Export-Csv -Path FileHashes.csv
```

## References
### Other good PowerShell Cheat Sheet
* [PowerShell Basic Cheat Sheet](http://ramblingcookiemonster.github.io/images/Cheat-Sheets/powershell-basic-cheat-sheet2.pdf) & [PowerShell Cheat Sheet](http://ramblingcookiemonster.github.io/images/Cheat-Sheets/powershell-cheat-sheet.pdf) by @ramblingcookiemonster
* [Cheat Sheet](https://gist.github.com/pcgeek86/336e08d1a09e3dd1a8f0a30a9fe61c8a) by @pcgeek86 
