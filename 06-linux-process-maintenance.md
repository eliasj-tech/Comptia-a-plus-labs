# Lab 06: Process Maintenance on Linux (ps, grep, and kill)

* **Date:** August 13, 2026
* **Objective:** Learn to monitor and filter active Linux system processes using `ps` and `grep`, identify unique Process IDs (PIDs), and terminate rogue root-level processes using the `kill` command.

---

## Part 1: Terminating a Specific Rogue Process

### Step 1.1: Locate the Target Process
* **What I Did:** I used the `ps -aux` command to list all running processes on the machine. Because this list is massively long, I used a pipe (`|`) to send that output directly into `grep` to filter for the specific process name `"totally_not_malicious"`.
  ```bash
  ps -aux | grep "totally_not_malicious"
  ```
* **Output Received:**
  ```text
  root       315  0.0  0.0   7232   520 ?        S    15:29   0:00 sudo nohup bash /home/totally_not_malicious
  root       320  0.0  0.1   3652   892 ?        S    15:29   0:00 bash /home/totally_not_malicious
  student   5431  0.0  0.1   3084   880 pts/0    S+   15:29   0:00 grep totally_not_malicious
  ```
* **Observation:** The command revealed two active malicious processes running under the `root` user with the PIDs **315** and **320**. The third line (PID 5431) was simply the `grep` search command I had just run.

### Step 1.2: Force-Terminate the Processes
* **What I Did:** Because these processes were owned by `root`, standard user permissions would not work. I used `sudo` to elevate my privileges and executed the `kill` command for both PIDs.
  ```bash
  sudo kill 315
  sudo kill 320
  ```

### Step 1.3: Verify Process Termination
* **What I Did:** I re-ran the filtered search command to confirm the processes were successfully stopped.
  ```bash
  ps -aux | grep "totally_not_malicious"
  ```
* **Output Received:**
  ```text
  student   5783  0.0  0.1   3084   884 pts/0    S+   16:15   0:00 grep totally_not_malicious
  ```
  *(Confirmed: The rogue processes are gone. Only the new `grep` search command appears in the output.)*

---

## Part 2: Terminating Multiple Processes Using Substrings

### Step 2.1: Substring Search using Grep
* **What I Did:** Unlike PowerShell which requires asterisk wildcards (`*razzle*`), Linux `grep` automatically searches for partial string matches. I searched for any process containing the word `"razzle"`.
  ```bash
  ps -aux | grep "razzle"
  ```
* **Output Received:**
  ```text
  root       328  0.0  0.6   7572  3616 ?        S    06:33   0:00 sudo nohup bash /home/razzle_dazzle
  root       329  0.0  0.5   7572  3480 ?        S    06:33   0:00 sudo nohup bash /home/my_cat_razzle
  root       330  0.0  0.5   7572  3520 ?        S    06:33   0:00 sudo nohup bash /home/razzles
  root       331  0.0  0.4   3648  2544 ?        S    06:33   0:00 bash /home/my_cat_razzle
  root       332  0.0  0.4   3648  2624 ?        S    06:33   0:00 bash /home/razzles
  root       333  0.0  0.4   3648  2680 ?        S    06:33   0:00 bash /home/razzle_dazzle
  student    773  0.0  0.1   3080   888 pts/0    S+   06:37   0:00 grep razzle
  ```
* **Observation:** The search successfully found six separate processes (PIDs 328 through 333) containing variations of the word "razzle".

### Step 2.2: Terminate Multiple Processes
* **What I Did:** I used `sudo kill` to systematically terminate each of the identified processes by their PIDs.
  ```bash
  sudo kill 328 329 330 331 332 333
  ```
  *(Note: You can string multiple PIDs together in a single `kill` command to save time).*

### Step 2.3: Verify Termination
* **What I Did:** I re-ran the `grep` substring search to verify all six processes were stopped.
  ```bash
  ps -aux | grep "razzle"
  ```
* **Output Received:**
  ```text
  student    870  0.0  0.1   3080   880 pts/0    S+   06:38   0:00 grep razzle
  ```

---

## Key Takeaways

* **The Power of Piping (`|`):** The `ps -aux` command generates too much noise on its own. Using the pipe operator to funnel that output into `grep` is an essential Linux skill for filtering logs, processes, and network connections.
* **The "Grep Inception":** Whenever you use `ps -aux | grep [name]`, the `grep` command itself will always appear in the search results because it is actively running in system memory at the exact moment it performs the search.
* **Sudo is Mandatory for System Processes:** Just like Windows requires "Run as Administrator," Linux requires `sudo` to terminate processes owned by other users or the `root` system.
