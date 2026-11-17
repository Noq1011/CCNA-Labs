# HP ProLiant DL380p Gen8 Ubuntu Server Setup Log

## Phase 1: Initial Power-Up and Hardware Check
* **Objective:** Power on the server, establish console access, and verify hardware components.
* **Steps Taken:**
  1. Plugged the server into power and connected the rack console (lap-drawer display and keyboard).
  2. Powered on the server and monitored the boot sequence screen.
     ![Initial POST screen](Images/IMG_2733.JPEG)
  3. *(Mistake Log)* Initially attempted to navigate the BIOS/boot prompts while completely forgetting to plug the physical keyboard USB cable in, resulting in frozen menus and non-responsive keystrokes. Once plugged in, keyboard control was restored.
     ![Rack console setup](Images/IMG_2735.JPEG)
  4. Observed the POST initialization and noted that the hard drive tray status LEDs flash briefly on boot and then go dark, accompanied by controller warning `1785-Slot 0 Drive Array Not Configured`.
     ![Smart Array controller warning](Images/IMG_2737.JPEG)

## Phase 2: Diagnosing Hard Drive LED and Power Issues
* **Objective:** Troubleshoot why the drive bay LEDs are not staying illuminated and check power delivery.
* **Steps Taken:**
  1. Evaluated power input: Tried bypassing the PDU and plugged the server power cord directly into a wall outlet to test if insufficient PDU power delivery was the root cause.
     ![Power cables and PDU setup](Images/IMG_2738.JPEG)
  2. Observed behavior: Drives flash briefly during initial power-on, then go dark because no valid logical array is configured on the Smart Array P420i controller.
     ![Front drive bays](Images/IMG_2740.JPEG)

## Phase 3: Configuring RAID for an API Workload via ACU
* **Objective:** Clear old configuration errors, group matching drives, and set up an optimal RAID array for an API server.
* **Steps Taken:**
  1. Booted into **Intelligent Provisioning** and opened the **Array Configuration Utility (ACU)**.
     ![Intelligent Provisioning home](Images/IMG_2748.JPEG)
  2. Identified 7 physical drives across two sizes: three 300GB SAS drives (Bays 1–3) and four 600GB SAS drives (Bays 5–8).
     ![Drive inventory list](Images/IMG_2746.JPEG)
  3. Selected the four 600GB drives to avoid capacity mismatches and proceeded to the **Create Logical Drive** screen.
     ![Logical drive configuration](Images/IMG_2745.JPEG)
  4. Configured fault tolerance (RAID level) and saved the array configuration to clear the `1785` error and initialize backplane power/LEDs.

## Phase 4: Ubuntu Server OS Installation & Custom Storage Partitioning
* **Objective:** Boot from the USB installer, clear out legacy layouts, and configure clean partitions on the newly created Smart Array RAID logical volume.
* **Steps Taken:**
  1. Inserted the purple Ubuntu Server USB into the front USB port of the ProLiant DL380p Gen8.
  2. Rebooted the server and pressed **F11** during POST to open the One-Time Boot Menu, selecting the USB drive.
  3. Launched **Install Ubuntu Server** from the GRUB boot menu.
  4. Configured language, keyboard layout, and network settings (verified DHCP lease assignment).
  5. Reached the **Storage configuration** screen and selected **Custom storage layout** for the 1.091T RAID volume.
     ![Storage configuration summary](Images/IMG_2744.JPEG)
  6. Configured custom partitions:
     * **Boot Partition (`/boot/efi`):** Set up the small boot partition and assigned the `/boot/efi` mount point.
     * **Root Partition (`/`):** Utilized the remaining space, formatted as `ext4`, and assigned the `/` mount point.
  7. Skipped optional featured server snaps to keep the initial server footprint lightweight for API hosting.
     ![Featured server snaps menu](Images/IMG_2749.jpg)
  8. Created user credentials (`rcsi_admin`) and enabled **OpenSSH server** installation for remote management.
  9. Completed installation, triggered the reboot prompt, and removed the USB drive to boot cleanly into the OS login prompt.
     ![Successful login and network IP binding](Images/IMG_2750.jpg)

## Phase 5: Verification and Server Automation
* **Objective:** Verify network connectivity, confirm SSH reachability, and apply bootstrap provisioning scripts.
* **Steps Taken:**
  1. Verified local interface binding (`ip a`) showing active IPv4 addressing on `eno4`.
  2. Created and tested automated provisioning using the `setup.sh` bootstrap script to handle package updates, essential tools (`git`, `curl`, `htop`), and UFW firewall hardening.
