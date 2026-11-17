# Cisco ISR 1100 Series Router Setup & Configuration Log

## Phase 1: Console Access & Password Recovery / Configuration Register
* **Objective:** Establish physical serial/console connection via PuTTY (`COM1`), enter ROMMON mode, and configure the boot register (`confreg 0x2142`) to bypass or reset old enterprise configurations.
* **Steps Taken:**
  1. Connected the rollover console cable from the workstation to the Cisco ISR 1100 series router console port and opened PuTTY.
  2. Powered on the router and interrupted the boot sequence to enter ROMMON mode (`rommon 1 >`).
  3. Adjusted the configuration register to bypass startup-config (`confreg 0x2142`) and issued the `reset` command to reboot clean.
     ![PuTTY ROMMON confreg setup](Images/1.JPG)
  4. Observed the boot logs and initialization of the IOS-XE microcode and hardware interfaces (`C1111-8PLTEEA`).
     ![IOS-XE boot logs](Images/3.JPG)

## Phase 2: Clearing Legacy Enterprise Configurations
* **Objective:** Clear out old persistent enterprise configurations (such as legacy UPS/network management banners or pre-existing credentials).
* **Steps Taken:**
  1. Booted past the bypassed configuration and encountered legacy login prompts/banners from prior deployments.
  2. Erased startup configs (`write erase`), reloaded, and verified clean factory-default initialization.
     ![Legacy configuration prompt cleanup](Images/2.JPG)

## Phase 3: Interface Configuration & IP Addressing
* **Objective:** Assign management and uplink IP addresses to physical router interfaces for switch/network connectivity.
* **Steps Taken:**
  1. Entered privileged EXEC mode (`enable`) and checked interface status using `show ip interface brief` to map active ports (`GigabitEthernet0/0/0` through `0/1/7`).
     ![Interface status check](Images/4.JPG)
  2. Entered global configuration mode (`config t`), selected interface `GigabitEthernet0/0/0`, assigned static IPv4 addressing (`198.0.209.117 255.255.255.248`), and brought the interface up (`no shutdown`).
  3. *(Mistake Log)* Attempted to issue an invalid `#reset` command from interface configuration mode rather than returning to global mode or reload, resulting in an `Invalid input detected` caret marker.
     ![Interface configuration and command syntax error](Images/5.JPG)