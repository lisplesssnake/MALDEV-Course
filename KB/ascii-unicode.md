# ascii vs unicode vs A vs W
The difference lies in **function name mapping in Windows headers**, which automatically decides whether to use the ANSI (`A`) or Unicode (`W`) version of the function based on your project's configuration.

Here's the explanation:

---

### **Why is there a `GetFileAttributes`?**

`GetFileAttributes` is a **macro**, not an actual function. It resolves to either `GetFileAttributesA` (ANSI) or `GetFileAttributesW` (Unicode) based on how your project is configured.

---

### **How Does It Work?**

- If your project is compiled with the `UNICODE` preprocessor definition, `GetFileAttributes` will map to `GetFileAttributesW`.
- If `UNICODE` is not defined (legacy behavior), `GetFileAttributes` will map to `GetFileAttributesA`.

This is a common pattern in the Windows API for compatibility between ANSI and Unicode systems.

---

### **Why Did It Cause an Issue?**

In the first snippet, you were using `char[]` for the file path (an ANSI string), but `GetFileAttributes` likely mapped to `GetFileAttributesW` (Unicode) because your project was configured for Unicode.

**Mismatch Problem:**

- `GetFileAttributesW` expects a `wchar_t*` (wide string), but you passed a `char[]` (ANSI string).
- This mismatch caused the function to fail, as the `char[]` string could not be interpreted as a valid `wchar_t*`.

---

### **How to Avoid This Issue**

1. **Be Explicit with ANSI/Unicode**
    
    - Use `GetFileAttributesA` if you’re working with ANSI strings.
    - Use `GetFileAttributesW` if you’re working with wide strings.
2. **Match the Project Settings**
    
    - If your project uses `UNICODE` by default, prefer wide strings (`wchar_t`) and Unicode APIs.
3. **Explicitly Specify the Macro Behavior**
    
    - If you want to ensure consistent behavior, you can disable `UNICODE` by defining `_MBCS` in your project or explicitly undefining `UNICODE`.

To check how your Visual Studio project is configured (ANSI or Unicode), follow these steps:

---

### **Check the Project Settings**

1. **Open Project Properties:**
    
    - In Visual Studio, right-click on your project in the **Solution Explorer** and select **Properties**.
2. **Locate the Character Set Setting:**
    
    - Go to **Configuration Properties** → **Advanced** → **Character Set**.
3. **Check the Value of Character Set:**
    
    - The available options are:
        - **Use Unicode Character Set**: Your project will use Unicode (`wchar_t`) and macros like `GetFileAttributes` will map to `GetFileAttributesW`.
        - **Use Multi-Byte Character Set**: Your project will use ANSI (`char`) and macros like `GetFileAttributes` will map to `GetFileAttributesA`.
        - **Not Set**: Defaults to the behavior of older Visual Studio versions (typically ANSI).

---

### **Check for Preprocessor Definitions**

1. **Go to Preprocessor Settings:**
    
    - In the same **Project Properties** window, navigate to:
        - **Configuration Properties** → **C/C++** → **Preprocessor** → **Preprocessor Definitions**.
2. **Look for `UNICODE` and `_UNICODE`:**
    
    - If `UNICODE` and `_UNICODE` are defined, your project is configured for Unicode, and macros will resolve to `W` versions of Windows API functions.
    - If these definitions are absent, your project defaults to ANSI, and macros will resolve to `A` versions.

---

### **Check Source Code Directives**

Sometimes, the project configuration can be overridden by preprocessor directives in the code itself. Look for these in your source files:

- **Forcing Unicode:**
    
    c
    
    Copy code
    
    `#define UNICODE #define _UNICODE`
    
- **Forcing ANSI:**
    
    c
    
    Copy code
    
    `#undef UNICODE #undef _UNICODE`
    

---

### **Determine What `GetFileAttributes` Resolves To**

To confirm which version of the function (`A` or `W`) is being used:

1. Add a quick check to your code:
    
    c
    
    Copy code
    
    `#ifdef UNICODE     printf("Using Unicode APIs (GetFileAttributesW)\n"); #else     printf("Using ANSI APIs (GetFileAttributesA)\n"); #endif`
    
2. Build and run your program. The output will tell you which character set is being used.
    

---

### **Switching Between ANSI and Unicode**

- **To Use Unicode:**
    
    - Set the **Character Set** in the project properties to **Use Unicode Character Set**.
    - Use `wchar_t` for strings and the `W` versions of Windows API functions (`CreateProcessW`, `GetFileAttributesW`, etc.).
- **To Use ANSI:**
    
    - Set the **Character Set** in the project properties to **Use Multi-Byte Character Set**.
    - Use `char` for strings and the `A` versions of Windows API functions (`CreateProcessA`, `GetFileAttributesA`, etc.).