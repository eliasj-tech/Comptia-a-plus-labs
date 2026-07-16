# Lab 02: Software Packaging and File Archiving on Linux

* **Date:** July 16, 2026
* **Objective:** Learn how to install and remove software in the Linux command line using low-level (`dpkg`) and high-level (`apt`) tools, work with `.tar` archive creation/extraction, and manage system package dependencies.

---

## Part 1: Installing Sublime Text & Troubleshooting Dependencies

### Step 1.1: Run the initial dpkg installation command
* **What I Did:** I ran the low-level Debian Package Manager to install the local `.deb` file:
  ```bash
  sudo dpkg -i /home/qwiklab/downloads/sublime-text_build-3211_amd64.deb
  ```

* **⚠️ The Gotcha Encountered:** The installation failed with a dependency error because `dpkg` cannot go out to the internet to download required companion software.
  ```text
  Selecting previously unselected package sublime-text.
  (Reading database ... 40291 files and directories currently installed.)
  Preparing to unpack .../sublime-text_build-3211_amd64.deb ...
  Unpacking sublime-text (3211) ...
  dpkg: dependency problems prevent configuration of sublime-text:
   sublime-text depends on libgtk-3-0; however:
    Package libgtk-3-0:amd64 is not installed.

  dpkg: error processing package sublime-text (--install):
   dependency problems - leaving unconfigured
  Processing triggers for hicolor-icon-theme (0.17-2) ...
  Errors were encountered while processing:
   sublime-text
  ```

### Step 1.2: Resolve the missing dependencies using APT
* **What I Did:** I used the high-level Advanced Package Tool (`apt`) with the `-f` (fix-broken) flag to automatically find and install the missing `libgtk-3-0` package.
  ```bash
  sudo apt install -f
  ```
* **System Prompt:** The terminal stopped and prompted me: *Do you want to continue? [Y/n]*. I typed `Y` and pressed Enter to complete the installation.

### Step 1.3: Verify Sublime Text is installed
* **What I Did:** I queried the package database to confirm the status of Sublime Text:
  ```bash
  dpkg -s sublime-text
  ```
* **Output Received:**
  ```text
  Package: sublime-text
  Status: install ok installed
  Priority: optional
  Section: editors
  Installed-Size: 34033
  Maintainer: Sublime HQ Pty Ltd <support@sublimetext.com>
  Architecture: amd64
  Version: 3211
  Depends: libgtk-3-0
  Description: Sublime Text is a sophisticated text editor for code, markup and prose
  ```

---

## Part 2: Working with Tar Archives

### Step 2.1: Navigate and extract a .tar archive
* **What I Did:** I navigated to the downloads directory and used `tar` to unpack `extract_me.tar` with verbose output enabled (`-xvf`):
  ```bash
  cd /home/qwiklab/downloads
  sudo tar -xvf extract_me.tar
  ```
* **Output Received (Extracted Files):**
  ```text
  home/qwiklab/extract_me/
  home/qwiklab/extract_me/great_job
  ```

### Step 2.2: Create a .tar archive using absolute paths
* **What I Did:** I returned to my home directory and archived three distinct files (`Earth`, `Mercury`, and `Venus`) into a single file named `Planets.tar` while preserving their system file paths:
  ```bash
  cd ~
  tar -cvf Planets.tar --absolute-names /home/qwiklab/documents/Earth /home/qwiklab/documents/Mercury /home/qwiklab/documents/Venus
  ```
* **Output Received (Archived Files):**
  ```text
  /home/qwiklab/documents/Earth
  /home/qwiklab/documents/Mercury
  /home/qwiklab/documents/Venus
  ```

---

## Part 3: Package Management with APT (7-Zip & GIMP)

### Step 3.1: Install 7-Zip via APT
* **What I Did:** I used `apt` to install `p7zip-full`.
  ```bash
  sudo apt install p7zip-full
  ```
* **System Alerts & Prompt:** The system notified me of unneeded packages and asked for space confirmation:
  ```text
  The following packages were automatically installed and are no longer required:
    liblua5.3-0 libvte-2.91-common
  Use 'sudo apt autoremove' to remove them.
  ...
  After this operation, 5787 kB of additional disk space will be used.
  Do you want to continue? [Y/n] Y
  ```
  *(I typed `Y` and pressed Enter to complete the setup.)*

### Step 3.2: Verify 7-Zip installation
* **What I Did:** I ran a query command to confirm the installation:
  ```bash
  dpkg -s p7zip-full
  ```
* **Output Received:**
  ```text
  Package: p7zip-full
  Status: install ok installed
  Priority: optional
  Section: utils
  Installed-Size: 4664
  Maintainer: Robert Luberda <robert@debian.org>
  Architecture: amd64
  Multi-Arch: foreign
  Source: p7zip
  Version: 16.02+dfsg-8
  ```

### Step 3.3: Uninstall GIMP via APT
* **What I Did:** I ran `apt` to remove GIMP, an image-editing program:
  ```bash
  sudo apt remove gimp
  ```
* **⚠️ The Gotcha Encountered (Orphaned Packages):** The terminal flagged a massive list of secondary libraries that were left behind as "no longer required" and advised me to run `sudo apt autoremove` later to free up extra space.
  ```text
  The following packages were automatically installed and are no longer required:
    alsa-topology-conf alsa-ucm-conf fonts-droid-fallback fonts-noto-mono ghostscript gimp-data graphviz gsfonts ... [truncated list of 100+ libraries]
  Use 'sudo apt autoremove' to remove them.
  
  The following packages will be REMOVED:
    gimp
  0 upgraded, 0 newly installed, 1 to remove and 0 not upgraded.
  After this operation, 22.8 MB disk space will be freed.
  Do you want to continue? [Y/n] Y
  ```
  *(I typed `Y` and completed the uninstallation.)*

### Step 3.4: Verify GIMP uninstallation
* **What I Did:** I ran a query to confirm GIMP was entirely uninstalled:
  ```bash
  dpkg -s gimp
  ```
* **Output Received:**
  ```text
  dpkg-query: package 'gimp' is not installed and no information is available
  ```

---

## Key Takeaways

* **The low-level vs. high-level package dynamic:** `dpkg` works directly with `.deb` files but cannot look outward to grab dependencies. `apt` coordinates with internet servers to automatically resolve failures when we use `apt install -f`.
* **The `autoremove` cleanup system:** Uninstalling a program like GIMP via `apt remove` only removes the main package wrapper, leaving behind hundreds of loose dependency libraries. Running `sudo apt autoremove` is critical for keeping the storage footprint clean.
* **The weight of `--absolute-names` in Tar:** Preserving absolute paths in `tar` makes sure files are packed and unpacked with their exact global directory paths (`/home/qwiklab/documents/`), preventing files from getting dropped into the wrong locations when extracted.
