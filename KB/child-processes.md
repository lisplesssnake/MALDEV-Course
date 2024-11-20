## How Child Processes Work in Windows
When a process spawns another process using APIs like CreateProcess, the new process (in this case, calc.exe) is a child process. The parent-child relationship means:

The child process (calc.exe) is initially associated with the parent process's environment.
The child process will remain running independently of the parent process unless explicitly controlled or terminated by the parent.
### Key Points About the Relationship
#### Independent Lifetime:

Even if the parent process (your program) exits, the child process (calc.exe) will continue running.
This is because calc.exe runs in its own process space, and its lifetime isn't tied to the parent unless explicitly managed.
#### Process Inheritance:

The child process inherits some attributes from the parent (e.g., handles, environment variables) if configured during its creation.
#### Ownership:

While calc.exe starts as a child of your process, it operates independently and isn't automatically terminated if your process ends.
