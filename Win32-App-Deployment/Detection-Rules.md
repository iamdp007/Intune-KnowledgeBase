# Detection Rules

## Overview

Detection Rules tell Microsoft Intune how to verify that an application has been installed successfully on a device.

If the application installs successfully but the detection rule is incorrect, Intune reports the deployment as **Failed**.

---

# Types of Detection Rules

## 1. File Detection

Checks whether a file or folder exists after installation.

### Example

```
Path

C:\Program Files\7-Zip

File

7z.exe
```

### Best Used When

- The application always installs to the same location.
- File existence is enough to confirm installation.

---

## 2. Registry Detection

Checks the Windows Registry for application information.

### Common Registry Locations

64-bit Applications

```
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
```

32-bit Applications

```
HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall
```

Current User Applications

```
HKCU\Software\Microsoft\Windows\CurrentVersion\Uninstall
```

### Common Registry Values

- DisplayName
- DisplayVersion
- Publisher
- InstallLocation
- UninstallString

### Best Used When

- Version tracking is required.
- Application writes proper uninstall information.

---

## 3. PowerShell Script Detection

Intune runs a PowerShell script to determine whether the application is installed.

### Example

```powershell
if (Test-Path "C:\Program Files\7-Zip\7z.exe")
{
    exit 0
}

exit 1
```

### Exit Codes

| Exit Code | Meaning |
|-----------|---------|
| 0 | Application Detected |
| 1 | Application Not Detected |

---

## 4. MSI Detection

For MSI applications, Intune can automatically detect the Product Code.

This is the simplest and most reliable detection method for MSI packages.

---

# Useful PowerShell Commands

## Check if a File Exists

```powershell
Test-Path "C:\Program Files\7-Zip\7z.exe"
```

---

## List Installed Applications

```powershell
Get-ItemProperty `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*", `
"HKLM:\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" |
Where-Object {$_.DisplayName}
```

---

## Find a Specific Application

```powershell
Where-Object {$_.DisplayName -like "*Firefox*"}
```

---

## Display Registry Information

```powershell
Select DisplayName, DisplayVersion, Publisher, UninstallString
```

---

# Detection Failure

## Error Code

```
0x87D1041C
```

### Meaning

```
Application Installed Successfully

↓

Detection Rule Failed

↓

Intune Reports Deployment as Failed
```

### Common Causes

- Incorrect file path.
- Incorrect registry path.
- Wrong DisplayVersion.
- Wrong detection method.
- Application installed in a different location.

---

# Best Practices

- Test the application manually before packaging.
- Verify the installation path.
- Verify registry entries after installation.
- Prefer Registry Detection when version tracking is required.
- Use File Detection only when appropriate.
- Test PowerShell detection scripts locally before uploading them to Intune.

---

# Practical Experience

During my Intune lab, I practiced:

- Successfully created File Detection for 7-Zip.
- Successfully used Registry Detection for Firefox.
- Successfully used Registry Detection for Notepad++.
- Used `Test-Path` to verify file existence.
- Troubleshot detection error **0x87D1041C**.
- Learned that an application can install successfully but still be reported as failed if the detection rule is incorrect.

---

# Key Takeaway

A Win32 application deployment is only considered successful when:

1. The application installs successfully.
2. The detection rule correctly identifies the application.

Both steps are equally important.
