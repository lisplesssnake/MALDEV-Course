# Shellcode exit strategy


### Problem identified
- when injecting shellcode in the current process, the program ignored the final `getchar()` that came after `CreateThread` and just ended after creating `calc.exe`

### Solution
- make sure you specify the `Exit Strategy` as thread when creating the shellcode with msfvenom
```
msfvenom -p windows/x64/exec CMD=calc.exe EXITFUNC=thread -f raw
```
- from what i understand this **should** be the default strategy, but looks like it's not

### Detailed explanation (ChatGPT)
The exit strategy determines what the shellcode does after it finishes its primary task (e.g., launching calc.exe). If the strategy isn't compatible with your program, it can cause unexpected behavior, such as terminating the thread or interfering with the main process's runtime environment.

#### Common msfvenom Exit Strategies
When generating shellcode with msfvenom, the EXITFUNC option controls the exit strategy. Here are the common options:

- thread (default for most payloads):

Exits the current thread without affecting the main process.
Suitable when you're injecting shellcode into a new thread (CreateThread).
- process:

Terminates the entire process.
Not suitable if you want your program to continue running after the shellcode.

- seh (Structured Exception Handling):

Uses structured exception handling to exit gracefully.
This is rarely used and might interfere with program flow if exceptions are mishandled.
- none:

No exit strategy; the behavior is unpredictable.
The shellcode will leave the process in an undefined state, often leading to crashes.
#### Why It Matters in Your Case
If you used `EXITFUNC=process`, the shellcode would terminate the entire process as soon as it completes, skipping the getchar() part. On the other hand, `EXITFUNC=thread` would ensure only the shellcode's thread exits, allowing the rest of your program to continue.

#### How to Specify the Exit Strategy
When creating shellcode with msfvenom, specify the EXITFUNC option explicitly. For example:


```bash
msfvenom -p windows/exec CMD=calc.exe EXITFUNC=thread -f c
```
In your case:

Use `EXITFUNC=thread` if you're running the shellcode in a thread created with CreateThread.
Avoid `EXITFUNC=process` unless you want to terminate the entire process intentionally.