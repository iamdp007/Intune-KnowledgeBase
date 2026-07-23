# Silent Install and Uninstall Switches

## Overview

Many applications support command-line switches for silent installation and uninstallation.

These switches are commonly used when deploying Win32 applications through Microsoft Intune.

Always verify the correct switch before packaging an application.

---

# Common Installer Types

## MSI

### Install

```cmd
msiexec /i app.msi /qn
```

### Uninstall

```cmd
msiexec /x {PRODUCT-GUID} /qn
```

### Common MSI Switches

| Switch | Purpose |
|---------|----------|
| /i | Install |
| /x | Uninstall |
| /qn | Quiet (No User Interface) |
| /qb | Basic UI |
| /norestart | Prevent Restart |
| /L*v log.txt | Create Installation Log |

---

## NSIS Installer

Usually supports

```cmd
/S
```

Examples

- Firefox
- 7-Zip
- Notepad++

---

## Inno Setup Installer

Usually supports

```cmd
/VERYSILENT
```

or

```cmd
/SILENT
```

Example

- WinSCP

---

## InstallShield Installer

Usually supports

```cmd
/s
```

---

# Applications Practiced During My Lab

## Firefox

Install

```cmd
Firefox Setup.exe /S
```

Uninstall

```cmd
helper.exe /S
```

---

## 7-Zip

Install

```cmd
7z2601-x64.exe /S
```

Uninstall

```cmd
Uninstall.exe /S
```

---

## Notepad++

Install

```cmd
npp.8.9.6.2.Installer.x64.exe /S
```

Uninstall

```cmd
uninstall.exe /S
```

---

## WinSCP

Install

```cmd
winscp.exe /VERYSILENT
```

Uninstall

```cmd
unins000.exe /VERYSILENT
```

---

## OpenVPN

Install

```cmd
OpenVPN.msi /qn
```

Uninstall

```cmd
msiexec /x {GUID} /qn
```

---

## PuTTY

Install

```cmd
putty.msi /qn
```

Uninstall

```cmd
msiexec /x {GUID} /qn
```

---

# How to Find Silent Switches

## Method 1

Run

```cmd
setup.exe /?
```

---

## Method 2

Run

```cmd
setup.exe /help
```

---

## Method 3

Run

```cmd
setup.exe -?
```

---

## Method 4

Run

```cmd
setup.exe -help
```

---

## Method 5

Read the vendor documentation.

---

## Method 6

Search online if documentation is unavailable.

---

# Best Practices

- Never assume every EXE uses `/S`.
- Always verify install and uninstall switches.
- Test the command manually before deploying through Intune.
- Record the working switches for future reference.

---

# Practical Experience

During my Intune lab I identified and used silent switches for:

- Firefox
- 7-Zip
- Notepad++
- WinSCP
- OpenVPN
- PuTTY

I also learned that different installer technologies use different silent switches.

---
# Finding Silent Install and Uninstall Switches

When packaging a new application, do not assume the silent switch.

Use the following methods to identify the correct install and uninstall commands.

---

## Method 1 - Display Installer Help (Recommended)

Open Command Prompt or PowerShell and navigate to the installer location.

Example:

```powershell
cd C:\Installers

.\example.exe /?
```

Try these common help options:

```powershell
.\example.exe /?

.\example.exe /help

.\example.exe -?

.\example.exe -help
```

These commands may display:

- Silent install switches
- Silent uninstall switches
- Logging options
- Restart options
- Other installer parameters

---

## Method 2 - Install the Application Manually

Install the application on a test machine.

After installation:

- Verify where it is installed.
- Locate the executable.
- Locate the uninstaller.
- Verify the installation folder.

---

## Method 3 - Check the Registry

List installed applications:

```powershell
Get-ItemProperty `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*", `
"HKLM:\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" |
Where-Object {$_.DisplayName}
```

Display uninstall information:

```powershell
Select DisplayName, DisplayVersion, UninstallString
```

Purpose:

- Find the uninstall command.
- Find the Product GUID.
- Find DisplayVersion.
- Find DisplayName.

---

## Method 4 - Search the Installation Folder

Look for uninstall executables.

Examples:

```
Uninstall.exe

unins000.exe

helper.exe

setup.exe
```

---

## Method 5 - Read Vendor Documentation

Many vendors publish installation guides with supported command-line switches.

---

## Method 6 - Search Online

If the installer doesn't provide help, search for:

```
<Application Name> silent install

<Application Name> command line switches

<Application Name> unattended install
```

---

# Practical Example

For Firefox:

```powershell
.\Firefox Setup.exe /?
```

If no help is displayed, install Firefox manually and check:

```powershell
"C:\Program Files\Mozilla Firefox\uninstall\helper.exe"
```

Registry:

```powershell
Get-ItemProperty `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" |
Where-Object {$_.DisplayName -like "*Firefox*"} |
Select DisplayName, UninstallString
```

Result:

```
"C:\Program Files\Mozilla Firefox\uninstall\helper.exe"
```

Silent uninstall:

```cmd
helper.exe /S
```

---

# Lesson Learned

Never package an application until you have confirmed:

- ✅ Silent install command
- ✅ Silent uninstall command
- ✅ Installation location
- ✅ Detection method
- ✅ Registry information

# Key Takeaway

Knowing the correct silent switch is the first step toward a successful Win32 application deployment.
