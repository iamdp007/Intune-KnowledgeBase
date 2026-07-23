# Application Path Finder

## Overview

This document contains PowerShell commands used to investigate an application before packaging it for Microsoft Intune.

These commands help identify:

- Installation path
- Executable name
- File version
- Registry information
- Product GUID
- Uninstall command
- Silent install/uninstall switches

---

# 1. Display Installer Help

Used to discover supported install and uninstall switches.

```powershell
.\setup.exe /?
```

```powershell
.\setup.exe /help
```

```powershell
.\setup.exe -?
```

```powershell
.\setup.exe -help
```

Purpose

- Find silent install switches
- Find uninstall switches
- View supported installer parameters

---

# 2. Get Complete File Information

```powershell
Get-Item "C:\Program Files\7-Zip\7z.exe" | Format-List *
```

Purpose

Displays all available information about the file.

Examples

- FullName
- Directory
- Name
- Length
- CreationTime
- LastWriteTime
- VersionInfo

---

# 3. Display Version Information

```powershell
(Get-Item "C:\Program Files\7-Zip\7z.exe").VersionInfo
```

or

```powershell
(Get-Item "C:\Program Files\7-Zip\7z.exe").VersionInfo | Format-List *
```

Purpose

Displays:

- ProductVersion
- FileVersion
- ProductName
- CompanyName
- OriginalFilename

---

# 4. Discover Object Properties (Get-Member)

One of the most useful PowerShell commands.

```powershell
Get-Item "C:\Program Files\7-Zip\7z.exe" | Get-Member
```

Example

```powershell
(Get-Item "C:\Program Files\7-Zip\7z.exe").VersionInfo | Get-Member
```

Purpose

Displays:

- Properties
- Methods
- Object Type

Use this whenever you want to know what information an object contains.

---

# 5. Locate an Executable

```powershell
Get-Command notepad.exe
```

or

```powershell
(Get-Command notepad.exe).Source
```

Purpose

Returns the executable path if it exists in the system PATH.

---

# 6. Search for an Executable

```powershell
Get-ChildItem "C:\Program Files" -Filter "*.exe" -Recurse
```

Search for a specific executable

```powershell
Get-ChildItem "C:\" -Filter "7z.exe" -Recurse
```

Purpose

Locate executable files on the system.

---

# 7. Verify File Exists

```powershell
Test-Path "C:\Program Files\7-Zip\7z.exe"
```

Returns

```
True
```

or

```
False
```

Purpose

Useful for Detection Rules and troubleshooting.

---

# 8. Display Installed Applications

```powershell
Get-ItemProperty `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*", `
"HKLM:\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" |
Where-Object {$_.DisplayName}
```

Purpose

Lists installed applications.

---

# 9. Find a Specific Application

```powershell
Get-ItemProperty `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*", `
"HKLM:\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" |
Where-Object {$_.DisplayName -like "*Firefox*"}
```

Purpose

Locate a specific application in the registry.

---

# 10. Display Registry Information

```powershell
Get-ItemProperty `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" |
Where-Object {$_.DisplayName -like "*Firefox*"} |
Format-List *
```

Purpose

Displays:

- DisplayName
- DisplayVersion
- Publisher
- InstallLocation
- UninstallString
- InstallDate

---

# 11. Display Uninstall Command

```powershell
Get-ItemProperty `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" |
Where-Object {$_.DisplayName -like "*Firefox*"} |
Select-Object DisplayName, UninstallString
```

Purpose

Retrieve the uninstall command stored in the registry.

---

# 12. Verify Digital Signature

```powershell
Get-AuthenticodeSignature "C:\Program Files\7-Zip\7z.exe"
```

Purpose

Verify that the executable is digitally signed.

---

# Practical Experience

During my Intune Win32 application lab, I used these commands to:

- Discover installation paths
- Find executable names
- Verify application versions
- Retrieve uninstall commands
- Identify Product GUIDs
- Build Detection Rules
- Troubleshoot Win32 application deployments

---

# Key Takeaway

Before packaging any application in Microsoft Intune, always investigate the application first.

A few minutes spent identifying the correct installer switches, registry information, executable paths, and uninstall commands can save hours of troubleshooting later.
## Practical Example - Using Variables with Get-Member

Store the object in a variable:

```powershell
$file = Get-Item "C:\Program Files\7-Zip\7z.exe"
```

Suppose you don't know what information `$file` contains.

Run:

```powershell
$file | Get-Member
```

PowerShell will display all available:

- Properties
- Methods
- Object Type

Some commonly used properties include:

- Name
- FullName
- Directory
- Length
- CreationTime
- LastWriteTime
- VersionInfo

You can then access individual properties, for example:

```powershell
$file.FullName
```

```powershell
$file.Directory
```

```powershell
$file.VersionInfo
```

To see everything inside the `VersionInfo` property:

```powershell
$file.VersionInfo | Get-Member
```

Or display all version information:

```powershell
$file.VersionInfo | Format-List *
```

### Why This Is Useful

When working with PowerShell, you often store command results in variables.

If you're unsure what a variable contains, use:

```powershell
<variable> | Get-Member
```

This helps you discover all the properties and methods available on that object.
