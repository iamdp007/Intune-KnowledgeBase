# Install Scripts

This document contains PowerShell install scripts used during Win32 application deployment.

---

## EXE Installation Script

```powershell
$exe = Join-Path $PSScriptRoot "setup.exe"

Start-Process -FilePath $exe -ArgumentList "/S" -Wait

exit 0
```

### Explanation

| Command | Purpose |
|---------|----------|
| `$PSScriptRoot` | Gets the current script folder |
| `Join-Path` | Combines folder and file name |
| `Start-Process` | Launches the installer |
| `-Wait` | Waits until installation finishes |
| `exit 0` | Returns Success to Intune |

---

## MSI Installation

```powershell
Start-Process "msiexec.exe" -ArgumentList "/i app.msi /qn" -Wait

exit 0
```

### MSI Switches

| Switch | Meaning |
|---------|----------|
| `/i` | Install |
| `/qn` | Quiet installation |
