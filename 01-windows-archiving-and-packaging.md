# Lab 01: Software Packaging and File Archiving on Windows

* **Date:** July 16, 2026
* **Objective:** Practice installing/removing software via Windows GUI and PowerShell, and manage compressed file formats (.tar and .zip).

---

## Part 1: GUI Installation (Sublime Text)
* **Goal:** Install Sublime Text using the Windows Graphical User Interface.
* **Steps Taken:** 1. Downloaded the installer (`sublime_text_build_xxx_x64_setup.exe`) via Google Chrome.
  2. Navigated to `C:\Users\qwiklabs\Downloads` and ran the executable.
  3. Walked through the installation wizard using default settings.
  4. Verified installation by launching Sublime Text via the Windows Search bar.

---

## Part 2: File Archiving & Extraction (GUI & CLI)

### 2.1 Extracting with 7-Zip (GUI Permissions Workaround)
* **Goal:** Extract the contents of `example.tar`.
* **The "Gotcha" Encountered:** I originally tried to extract the file inside the `Downloads` folder, but Windows threw a permissions error.
* **The Resolution:** I dragged `example.tar` to the **Desktop** first, which bypassed the permission restriction. Then, I right-clicked -> **7-Zip** -> **Extract Here**.

### 2.2 Compressing with PowerShell (CLI)
* **Goal:** Archive three files (`Earth`, `Mercury`, and `Venus`) into a single compressed file called `Planets.zip`.
* **Commands Used:**
  * First, I opened PowerShell as **Administrator** and changed directories:
    ```powershell
    cd C:\Users\Qwiklab\Documents\
    ```
  * Next, I ran the compression command:
    ```powershell
    Compress-Archive -Path Earth, Mercury, Venus Planets.zip
    ```

---

## Part 3: CLI Software Management (PowerShell)

### 3.1 Installing VLC Media Player
* **Goal:** Download and silently install VLC Media Player using a PowerShell script block.
* **Commands Used:**
  *(Note: I ran a script to fetch the latest .exe installer and execute it silently via the command line)*
  ```powershell
  $VLC_URL = "[https://get.videolan.org/vlc/last/win64/](https://get.videolan.org/vlc/last/win64/)"
  $GET_HTML = Invoke-WebRequest $VLC_URL$FILE = $GET_HTML.Links \vert{} Select-Object @{Label='href';Expression={@{$true=$_.href}[$_.href.EndsWith('win64.exe')]}} | Select-Object -ExpandProperty href
  $URL = ($VLC_URL+$FILE)$DOWNLOAD_DIR = "C:\users\qwiklabs\Downloads\"
  $OUTPUT_FILE = ($DOWNLOAD_DIR+$FILE)
  (new-object System.Net.WebClient).DownloadFile($URL,$OUTPUT_FILE)
  cmd.exe /c $OUTPUT_FILE /S
