# Install Scripts This document contains PowerShell install scripts used during Win32 application deployment. --- ## EXE Installation Script
powershell

$exe = Join-Path $PSScriptRoot "setup.exe"



Start-Process -FilePath $exe -ArgumentList "/S" -Wait



exit 0
### Explanation | Command | Purpose | |---------|----------| | $PSScriptRoot | Gets the current script folder | | Join-Path | Combines folder and file name | | Start-Process | Launches the installer | | -Wait | Waits until installation finishes | | exit 0 | Returns Success to Intune | --- ## MSI Installation
powershell

Start-Process "msiexec.exe" -ArgumentList "/i app.msi /qn" -Wait



exit 0
### MSI Switches | Switch | Meaning | |---------|----------| | /i | Install | | /qn | Quiet installation |

## PowerShell Script Installation

Many Win32 applications use PowerShell scripts instead of directly calling an EXE or MSI.

### Basic PowerShell Script

```cmd
powershell.exe -ExecutionPolicy Bypass -File "Install.ps1"
```

### Example

```cmd
powershell.exe -ExecutionPolicy Bypass -File "Stickies Install.ps1"
```

### Explanation

| Parameter | Purpose |
|-----------|----------|
| `powershell.exe` | Starts the PowerShell engine |
| `-ExecutionPolicy Bypass` | Temporarily bypasses the execution policy for this session |
| `-File` | Executes the specified PowerShell script |

### When to Use

Use a PowerShell installation script when:

- Installing multiple applications.
- Performing pre-installation checks.
- Creating folders or registry keys.
- Copying files before or after installation.
- Running custom installation logic.

### Example Install Command in Intune

```cmd
powershell.exe -ExecutionPolicy Bypass -File "Install.ps1"
```

### Best Practice

- Store the PowerShell script inside the `.intunewin` package.
- Use `$PSScriptRoot` to reference files within the package.
- Always return `exit 0` for success and a non-zero exit code for failure.
