#windows 

###### <span class="green-highlight-light">User and Kernel Modes</span>

>[!insight]
>Link to Microsoft documentation: [link](https://docs.microsoft.com/en-us/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)

###### <span class="green-highlight-light">System</span>

Normal behavior:
* **Parent process** - N/A or System Idle Process (0)
* **Number of instances** - 1
* **PID** - 4
* **Start Time** - boot time
* **User account** - local system

Always has a PID of 4.

> [!quote]
> The System process (process ID 4) is the home for a special kind of thread that runs only in kernel mode a kernel-mode system thread. System threads have all the attributes and contexts of regular user-mode threads (such as a hardware context, priority, and so on) but are different in that they run only in kernel-mode executing code loaded in system space, whether that is in Ntoskrnl.exe or in any other loaded device driver. In addition, system threads don't have a user process address space and hence must allocate any dynamic storage from operating system memory heaps, such as a paged or nonpaged pool.

###### <span class="green-highlight-light">System > smss.exe</span>

Normal behavior:
* **Parent process** - System (4)
* **Number of instances** - One master instance and child instance per session. The child instance exits after creating the session.
* **Start Time** - Within seconds of boot time for the master instance
* **User account** - local system (SYSTEM)
* **Image path** - %SystemRoot%\System32\smss.exe

Session Manager Subsystem (smss) or Windows Session Manager, is responsible for creating new sessions. It is the first user-mode process started by the kernel. This process starts the kernel and user modes of the Windows subsystem (you can read more about the NT Architecture [here](https://en.wikipedia.org/wiki/Architecture_of_Windows_NT)). This subsystem includes **win32k.sys** (kernel mode), winsrv.dll (user mode), and csrss.exe (user mode).

Smss.exe starts **csrss.exe** (Windows subsystem) and **wininit.exe** in Session 0, an isolated Windows session for the operating system, and **csrss.exe** and **winlogon.exe** for Session 1, which is the user session. The first child instance creates child instances in new sessions, done by smss.exe copying itself into the new session and self-terminating. You can read more about this process [here](https://en.wikipedia.org/wiki/Session_Manager_Subsystem).

![[Pasted image 20250619114744.png]]

Any other subsystem listed in the `Required` value of `HKLM\System\CurrentControlSet\Control\Session Manager\Subsystems` is also launched. SMSS is also responsible for creating environment variables, virtual memory paging files and starts winlogon.exe (the Windows Logon Manager).

###### <span class="green-highlight-light">csrss.exe</span>

Normal behavior:
* **Parent process** - Created by an instance of smss.exe. Will show as a non-existent process as smss.exe terminates itself after creating csrss.exe.
* **Number of instances** - two or more
* **Start Time** - Within seconds of boot time for the first two instances (for Session 0 and 1). Start times for additional instances occur as new sessions are created, although only Sessions 0 and 1 are often created.
* **User account** - local system (SYSTEM)
* **Image path** - %SystemRoot%\System32\csrss.exe

Client Server Runtime Process is the user-mode side of the Windows subsystem. This process is always running and is critical to system operation. If this process is terminated by chance, it will result in system failure. This process is responsible for the Win32 console window and process thread creation and deletion. For each instance, csrsrv.dll, basesrv.dll, and winsrv.dll are loaded (along with others).

This process is also responsible for making the Windows API available to other processes, mapping drive letters, and handling the Windows shutdown process.

>[!extra]
>Additional information about this process can be found [here](https://en.wikipedia.org/wiki/Client/Server_Runtime_Subsystem)

###### <span class="green-highlight-light">wininit.exe</span>

Normal behavior:
* **Parent process** - Created by an instance of smss.exe. Will show as a non-existent process as smss.exe terminates itself after creating csrss.exe.
* **Number of instances** - two or more
* **Start Time** - Within seconds of boot time for the first two instances (for Session 0 and 1). Start times for additional instances occur as new sessions are created, although only Sessions 0 and 1 are often created.
* **User account** - local system (SYSTEM)
* **Image path** - %SystemRoot%\System32\csrss.exe