# Lab 05: Process Maintenance on Windows (PowerShell & Task Management)

* **Date:** August 11, 2026
* **Objective:** Collect process resource utilization data, locate rogue processes by exact name and wildcard patterns, force-terminate processes by PID via PowerShell, and verify termination status.

---

## Part 1: Terminating a Specific Rogue Process

### Step 1.1: Open PowerShell as Administrator
* **What I Did:** I searched for **Windows PowerShell** in the Start Menu, right-clicked it, and selected **Run as Administrator** to ensure the session had administrative privileges required to kill system processes.

### Step 1.2: Locate the Target Process
* **What I Did:** I used `Get-Process` to query the system for a specific running process named `"totally_not_malicious"`.
  ```powershell
  Get-Process -Name "totally_not_malicious"
  ```
* **Output Received:**
  ```text
  Handles  NPM(K)   PM(K)    WS(K)   CPU(s)     Id   SI ProcessName
  -------  ------   -----    -----   ------     --   -- -----------
      209      14    2724    10996   271.06   7164    1 totally_not_malicious
  ```
* **Observation:** The command successfully isolated the process and identified its unique Process ID (**PID: 7164**).

### Step 1.3: Force-Terminate the Process by PID
* **What I Did:** I used `taskkill` with the `/F` (Force) and `/PID` (Process ID) flags to terminate the process using its ID.
  ```powershell
  taskkill /F /PID 7164
  ```
* **Output Received:**
  ```text
  SUCCESS: The process with PID 7164 has been terminated.
  ```

### Step 1.4: Verify Process Termination
* **What I Did:** I re-ran the `Get-Process` command to confirm the process was no longer active.
  ```powershell
  Get-Process -Name "totally_not_malicious"
  ```
* **⚠️ The Gotcha Encountered (Exact Match Error):** Because an exact string lookup was used, PowerShell threw an error confirming the process no longer exists on the system.
  ```text
  Get-Process : Cannot find a process with the name "totally_not_malicious". Verify the process name and call the cmdlet again.
  At line:1 char:1
  + Get-Process -Name "totally_not_malicious"
  + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      + CategoryInfo          : ObjectNotFound: (totally_not_malicious:String) [Get-Process], ProcessCommandException
      + FullyQualifiedErrorId : NoProcessFoundForGivenName,Microsoft.PowerShell.Commands.GetProcessCommand
  ```

---

## Part 2: Terminating Multiple Processes Using Wildcards

### Step 2.1: Pattern Search using Wildcards
* **What I Did:** Since `Get-Process` does not perform partial-string matches by default, searching for `"razzle"` would yield zero results. I used asterisk wildcards (`*`) to search for any process containing "razzle" in its name.
  ```powershell
  Get-Process -Name "*razzle*"
  ```
* **Output Received:**
  ```text
  Handles  NPM(K)    PM(K)      WS(K)      CPU(s)     Id  SI ProcessName
  -------  ------    -----      -----      ------     --  -- -----------
      209      14     2784      11028      572.89   2120   1 my_cat_razzle
      209      14     2824      11036      572.83   7052   1 razzle_dazzle
  ```
* **Observation:** The wildcard query identified two running processes: `my_cat_razzle` (**PID: 2120**) and `razzle_dazzle` (**PID: 7052**).

### Step 2.2: Terminate Multiple Processes
* **What I Did:** I executed `taskkill` sequentially for each discovered PID to terminate both processes.
  ```powershell
  taskkill /F /PID 2120
  taskkill /F /PID 7052
  ```
* **Output Received:**
  ```text
  PS C:\users\qwiklabs> taskkill /F /PID 2120
  SUCCESS: The process with PID 2120 has been terminated.
  PS C:\users\qwiklabs> taskkill /F /PID 7052
  SUCCESS: The process with PID 7052 has been terminated.
  ```

### Step 2.3: Verify Termination with Wildcard Query
* **What I Did:** I re-ran the wildcard search to verify both processes were stopped.
  ```powershell
  Get-Process -Name "*razzle*"
  ```
* **Key Behavior Observed:** Unlike the exact match search in Part 1 (which threw an error), a wildcard search returns an empty prompt when no matching processes exist.
  ```text
  PS C:\users\qwiklabs> Get-Process -Name "*razzle*"
  PS C:\users\qwiklabs> 
  ```

---

## Key Takeaways

* **Targeting by PID vs Name:** While you can kill processes by name, targeting by Process ID (`PID`) using `taskkill /F /PID [ID]` is safer in enterprise environments to ensure you only kill the specific frozen instance without killing other legitimate instances of the same application.
* **Wildcard vs. Exact Match Output Dynamics:** 
  * `Get-Process -Name "ExactName"` throws a `ProcessCommandException` error if the process is not found.
  * `Get-Process -Name "*Wildcard*"` silently returns an empty output line when no matches exist.
* **Administrative Privileges:** Standard user accounts cannot kill processes owned by other users or the system. Running PowerShell with elevated permissions (`Run as Administrator`) is required for effective process management.
