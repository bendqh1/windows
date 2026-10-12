## 1. Create the Script

1. Right-click on your Desktop → **New** → **AutoHotkey Script**.
2. Name the file `MaximizeNotepad.ahk`.
3. Right-click the file → **Edit script**.
4. Delete any existing content and paste the following code:

```ahk
#Requires AutoHotkey v2.0
#SingleInstance Force

; Force modern Windows 11 Notepad to maximize whenever it opens
SetTimer(MaximizeNotepad, 250)

MaximizeNotepad() {
    if WinExist("ahk_exe Notepad.exe") {
        state := WinGetMinMax("ahk_exe Notepad.exe")
        if (state != 1) {          ; 1 = already maximized
            WinMaximize("ahk_exe Notepad.exe")
        }
    }
}
```

Save the file and close the editor.

---

## 2. Run the Script

- Double-click `MaximizeNotepad.ahk`.
- A green **H** icon will appear in the system tray — the script is now active.
- Open Notepad from the taskbar (or any other method). It should maximize automatically within a fraction of a second.

---

## 3. Make the Script Start Automatically with Windows

1. Press `Win + R`, type `shell:startup`, and press Enter.
2. Copy `MaximizeNotepad.ahk` into the Startup folder that opens.
