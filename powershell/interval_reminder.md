On drive C, create a file named `interval_reminder.ps1` and paste there:

```powershell
Add-Type -AssemblyName System.Windows.Forms
Add-Type -AssemblyName System.Drawing

$form = New-Object System.Windows.Forms.Form
$form.Text = "Reminder"
$form.Size = New-Object System.Drawing.Size(600,200)
$form.TopMost = $true

$label = New-Object System.Windows.Forms.Label
$label.Text = "It's the day to take a haircut today!"
$label.Font = New-Object System.Drawing.Font("Segoe UI",20)
$label.AutoSize = $true
$label.Location = New-Object System.Drawing.Point(20,50)

$form.Controls.Add($label)

[void]$form.ShowDialog()
```

## Testing the script file manually

```powershell
powershell -STA -ExecutionPolicy Bypass -File "C:\interval_reminder.ps1"
```

## Code tasks

### Logon

```powershell
schtasks /Create /TN "HaircutReminder_Logon" /SC ONLOGON /F ^
/TR "powershell -STA -ExecutionPolicy Bypass -File C:\interval_reminder.ps1"
```

### 72 hours interval

```powershell
schtasks /Create /TN "HaircutReminder_72h" /SC HOURLY /MO 72 /F ^
/TR "powershell -STA -ExecutionPolicy Bypass -File C:\interval_reminder.ps1"
```

## Stopping the tasks

schtasks /Delete /TN "HaircutReminder_Logon" /F
schtasks /Delete /TN "HaircutReminder_72h" /F

## Notes

`/SC MINUTE /MO 1` means 1 minute but if we want it to run, say, every 72 hours we could change that to `/SC HOURLY /MO 72`.
