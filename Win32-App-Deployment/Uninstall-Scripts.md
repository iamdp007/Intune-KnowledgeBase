# Uninstall Scripts

This document contains PowerShell uninstall scripts used during Win32 application deployment.

---

## EXE Uninstall Script

```powershell
Start-Process "C:\Program Files\App\Uninstall.exe" -ArgumentList "/S" -Wait

exit 0
```

### Explanation

| Command | Purpose |
|---------|----------|
| Start-Process | Starts the uninstall process |
| /S | Silent uninstall (application-specific) |
| -Wait | Wait until uninstall completes |
| exit 0 | Reports success to Intune |

---

## MSI Uninstall Script

```powershell
Start-Process "msiexec.exe" -ArgumentList "/x {PRODUCT-GUID} /qn" -Wait

exit 0
```

### MSI Switches

| Switch | Meaning |
|---------|----------|
| /x | Uninstall |
| /qn | Quiet uninstall |
| {PRODUCT-GUID} | MSI Product Code |

---

## Finding the Uninstall Command

### Registry Locations

64-bit Applications

```text
HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
```

32-bit Applications

```text
HKLM:\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall
```

Current User Applications

```text
HKCU:\Software\Microsoft\Windows\CurrentVersion\Uninstall
```

---

## PowerShell Command

List installed applications:

```powershell
Get-ItemProperty `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*", `
"HKLM:\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" |
Where-Object {$_.DisplayName} |
Select-Object DisplayName, UninstallString
```

---

## Examples from My Lab

| Application | Uninstall Command |
|-------------|-------------------|
| Firefox | helper.exe /S |
| 7-Zip | Uninstall.exe /S |
| Notepad++ | uninstall.exe /S |
| WinSCP | unins000.exe /VERYSILENT |
| OpenVPN | msiexec /x {GUID} /qn |
| PuTTY | msiexec /x {GUID} /qn |

---

## Best Practices

- Test the uninstall command manually before using it in Intune.
- ---

## PowerShell Script Uninstall

Many Win32 applications use PowerShell scripts instead of directly calling an EXE or MSI for uninstallation.

### Basic PowerShell Script

```cmd
powershell.exe -ExecutionPolicy Bypass -File "Uninstall.ps1"
```

### Example

```cmd
powershell.exe -ExecutionPolicy Bypass -File "Stickies Uninstall.ps1"
```

### Explanation

| Parameter | Purpose |
|-----------|----------|
| `powershell.exe` | Starts the PowerShell engine |
| `-ExecutionPolicy Bypass` | Temporarily bypasses the execution policy for this session |
| `-File` | Executes the specified PowerShell script |

### When to Use

Use a PowerShell uninstallation script when:

- Uninstalling applications that require multiple steps.
- Running cleanup tasks after the uninstall.
- Removing registry keys.
- Deleting files and folders.
- Stopping services or processes before uninstalling.
- Running custom PowerShell logic.

### Example Uninstall Command in Intune

```cmd
powershell.exe -ExecutionPolicy Bypass -File "Uninstall.ps1"
```

Example used during my lab:

```cmd
powershell.exe -ExecutionPolicy Bypass -File "Stickies Uninstall.ps1"
```

### Best Practice

- Store the PowerShell script inside the `.intunewin` package.
- Use `$PSScriptRoot` to locate uninstall files included in the package.
- Return `exit 0` when the uninstall is successful.
- Return a non-zero exit code if the uninstall fails.
- Use the registry to identify uninstall commands and Product GUIDs.
- Verify that the application is completely removed before considering the uninstall successful.
