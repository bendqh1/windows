## My problem

In Windows 11 Home, a specific update wouldn't install for years, whilst other updates get installed regularly.

I keep getting a notification about the need to update Windows and it leads to the following message:

> Your device is missing important security and quality fixes. Make sure to keep your device on and plugged in so updates can complete.

## The cause of the problem

Probably, the cause of the problem is that the specific personal computer I work with is a bit old, a **Dell Latitude 5580** from year 2017.

## What I have tried to solve the problem

### Hiding the update icon in the system tray

Just to make the `system tray` icon of update notifications go away, I right-clicked on the icon and then left-clicked on "Hide for now". This made the notification go away, but only until next reset, then it came back to appear.

### Run Powershell as Administrator

```powershell
Install-Module PSWindowsUpdate -Force
Import-Module PSWindowsUpdate
```

If asked to install *NuGet* than install it because NuGet is a Microsoft package manager required to download PowerShell modules like `PSWindowsUpdate`. It's safe and necessary in this context.

### Hiding the update

```powershell
Hide-WindowsUpdate -Title "hp usb 12/10/2018 12:00 AM - 49.0.4411.18331"
```

* If getting an error about the lack of ability to run scripts due to to Powershell execution policy, then to  proceed safely temporarily allow scripts for your current session only:

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
```

Then run the command to hide the update.

### Confirm result

```powershell
Get-WindowsUpdate -IsHidden
```

Something like this sould be put out:

```
ComputerName Status     KB          Size Title
------------ ------     --          ---- -----
X          -D--H--                14MB UPDATE_TITLE_COMES_HERE
```

`-D--H--` means "Declined" and "Hidden".

### Reset Windows update components

After resetting I get:

> Your device is missing important security and quality fixes. Make sure to keep your device on and plugged in so updates can complete.

```powershell
net stop wuauserv
net stop cryptSvc
net stop bits
net stop msiserver
ren C:\Windows\SoftwareDistribution SoftwareDistribution.old
ren C:\Windows\System32\catroot2 catroot2.old
net start wuauserv
net start cryptSvc
net start bits
net start msiserver
```

No errors where given.

### Open `Run` and execute the following

```
ms-settings:troubleshoot
```

Theen do:

**Other troubleshooters** > **Windows Update** > **Run** (click "Run" button there).

### Open CMD as Administrator

```cmd
usoclient StartScan
```

No output was given.

Or:

```cmd
wuauclt /detectnow /updatenow
```

No output was given.

### Deployment Image Servicing and Management (DISM)

```cmd
DISM /Online /Cleanup-Image /CheckHealth
DISM /Online /Cleanup-Image /ScanHealth
DISM /Online /Cleanup-Image /RestoreHealth
```

No corruption was found anywhere.

### Resetting Windows.

Windows update troubleshooter finds a problem but doesn't fix it.

Doing a full Windows reset via **Settings** > **System** > **Recovery** and then under Recovery options, click **Reset this PC** > **Remove everything** > **Local reinstall** didn't help at all. The original problem persists.

## How to solve that problem?

Short answer without waffling. No emojis and no colorful characters. Just a short explanation about what to do.

# Attempt 1

## Download and install Windows 11 in the latest version directly - without a boot or with a boot

* Download a ~7GB ISO of Windows 11.
* Mount the ISO by opening it like opening an archive file (right-click > Mount).
* Click `setup.exe` and install Windows 11 without a boot.

If this isn't enough, a boot installation is required.

## If a processor is too old for a newer version of Windows 11

If, according CPU/TPM checks,  the processor is too old to install the newest version of Windows 11 without a boot then try the following:

1. **Mount the ISO**.
2. **Copy all files** from the mounted ISO to a new folder on your desktop, e.g. `Win11_Bypass`.
3. Open **Notepad** and paste:

 ```reg
 Windows Registry Editor Version 5.00

 [HKEY_LOCAL_MACHINE\SYSTEM\Setup\MoSetup]
 "AllowUpgradesWithUnsupportedTPMOrCPU"=dword:00000001
 ```

4. Save it as `bypass.reg` on your desktop.  
5. **Double-click `bypass.reg`** to apply the registry tweak. Click Yes when prompted.  
6. Go to the `Win11_Bypass` folder and **run `setup.exe`**.  
7. Proceed with the **in-place upgrade**, choosing **Keep personal files and apps**.  

## Failure

Windows 11 setup is stuck at the "Getting updates" phase, at 46% for 30 minutes, even though internet works and stable.

We can try to run `setup.exe` with `/compat IgnoreWarning` and `/dynamicupdate disable` flags via Command Prompt:

### Find Desktop dir:

```cmd
reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders"
```

### Reopen CMD as Administrator

```cmd
cd DESKTOP_WHEREVER_IT_IS
cd Win11_Bypass
setup.exe /compat IgnoreWarning /dynamicupdate disable
```
