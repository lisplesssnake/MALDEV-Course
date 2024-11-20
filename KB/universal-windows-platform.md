# **Modern UWP (Universal Windows Platform) apps** in Windows. 
Microsoft has transitioned some classic system utilities, like the Calculator, notepad, paint, snipping tool, media player, file explorer, photos to UWP apps. Here's a breakdown of what's happening:

---

### What’s Happening Behind the Scenes

1. **Your Shellcode or Code Starts `calc.exe`**:
    
    - The original `calc.exe` is a legacy executable located at `C:\Windows\System32\calc.exe`.
    - When you or your shellcode runs `calc.exe`, it launches as a child of your process (`myproc.exe`).
2. **Redirection to the UWP App**:
    
    - The legacy `calc.exe` acts as a **stub or launcher** for the modern UWP Calculator app.
    - As soon as `calc.exe` starts, it redirects the operation to the UWP version of Calculator located in the `C:\Program Files\WindowsApps` folder.
3. **Spawning the UWP Calculator**:
    
    - The UWP Calculator (`CalculatorApp.exe`) is started by `sihost.exe` (Shell Infrastructure Host), which is responsible for managing UWP apps and the modern Windows interface.
    - `sihost.exe` itself is often started and managed by `svchost.exe`, the Service Host process.
4. **Termination of `calc.exe`**:
    
    - Once the UWP Calculator app is successfully launched, the original `calc.exe` process exits almost immediately, leaving only the UWP app running.

### Process Hierarchy

- **Your Process (`myproc.exe`)**
    - Starts `calc.exe` (`C:\Windows\System32\calc.exe`).
        - The child process exists briefly and then exits.
    - Meanwhile, `sihost.exe` (managed by `svchost.exe`) spawns the modern UWP `CalculatorApp.exe`.

So, the UWP `CalculatorApp.exe` you see under `sihost.exe` is the actual running Calculator app.

---

### Why This Happens

1. **Backward Compatibility**:
    
    - Older applications or scripts that rely on `calc.exe` still work without modification.
    - Windows keeps the `calc.exe` stub for compatibility, but it no longer contains the actual calculator logic.
2. **UWP Transition**:
    
    - Microsoft wants users to use the UWP version for its enhanced features and better integration with the modern Windows ecosystem.
    - UWP apps are managed differently—they run in a sandboxed environment and are handled by `sihost.exe`.
3. **App Location**:
    
    - UWP apps are installed in `C:\Program Files\WindowsApps\`, a special protected folder for modern apps.

### Verifying the Behavior

To observe this redirection:

1. **Check `calc.exe` Execution**:
    
    - Use a tool like **Process Monitor (ProcMon)** from Sysinternals to monitor `calc.exe`.
    - You'll see that `calc.exe` starts and then launches `CalculatorApp.exe` before exiting.
2. **Inspect UWP Apps**:
    
    - Open `C:\Program Files\WindowsApps` and locate the `Microsoft.WindowsCalculator_*` folder.
    - You’ll find `CalculatorApp.exe` there, which is the modern app.
3. **Use Command-Line Tools**:
    
    - Run `calc.exe` from a command prompt and observe the brief appearance of `calc.exe` in Task Manager before it redirects to the UWP version.

---

### How to Launch Just `calc.exe`

If you want to prevent this redirection and only run the classic `calc.exe` without invoking the UWP app, you can:

1. **Copy `calc.exe` to a Non-System Folder**:
    
    - Copy `C:\Windows\System32\calc.exe` to a different location.
    - Run the copied executable directly.
2. **Disable the UWP App**:
    
    - This involves modifying or uninstalling the UWP Calculator app, which might not be recommended.

---

### Summary

- **`calc.exe` in `System32`**: Acts as a stub for compatibility.
- **UWP `CalculatorApp.exe`**: The actual modern calculator app, managed by `sihost.exe`.
- What you're seeing is normal for modern Windows systems and reflects the transition to UWP apps.