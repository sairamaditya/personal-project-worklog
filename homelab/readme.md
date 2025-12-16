
***

# Homelab Setup

This document captures the steps taken to rescue an HP t540 thin client from a disk error and turn it into an Ubuntu-based homelab node, plus the estimated hardware cost.

***

## 1. Recovering from “No boot disk has been detected or the disk has failed”

1. Power on the HP t540 and observe the error:  
   `ERROR: No boot disk has been detected or the disk has failed.`
2. Interpret this as: the firmware cannot find a bootable OS on any attached drive, either because:
   - The internal disk is missing, not detected, or has failed.  
   - The boot order is wrong (e.g., trying network/USB first with no valid media).  
   - The existing OS/bootloader is corrupted.
3. Plan: install a fresh OS (Ubuntu) from a USB stick and treat the device as a new homelab node.

***

## 2. Creating an Ubuntu USB installer on macOS

### 2.1 Download Ubuntu ISO

1. On a Mac, connect an empty USB flash drive (8 GB or larger).
2. Open a browser and go to the official Ubuntu Desktop download page.
3. Download the latest LTS 64‑bit Ubuntu Desktop ISO (e.g., `ubuntu-24.04-desktop-amd64.iso`).
4. Wait for the download to complete (ISO will usually be 5–6 GB).

### 2.2 Install a USB flashing tool (balenaEtcher)

1. On macOS, download and install **balenaEtcher**.
2. Move the application to `/Applications`.
3. Launch the app; grant any requested security permissions.

### 2.3 Flash the ISO to USB

1. Back up any important data from the USB drive (it will be erased).
2. In balenaEtcher:
   - Click **Flash from file** and select the downloaded Ubuntu ISO.  
   - Click **Select target** and choose the USB drive by capacity/name.  
   - Click **Flash** and enter the Mac password when asked.
3. Wait until the flashing and verification completes successfully.
4. Eject the USB drive from Finder, then physically unplug it.

This did not work so used windows, steps are provided below

### 2.4 Handling Etcher stuck around ~48%

If Etcher appears stuck at ~48%:

1. Give it a few minutes; large ISOs and slow USB drives can appear to stall.
2. If progress is frozen for 10–15 minutes:
   - Cancel or force‑quit Etcher.
   - Remove and reinsert the USB.
3. Open **Disk Utility**:
   - Select the USB **device** (top level).
   - Erase it as `MS-DOS (FAT)` or `ExFAT` with `GUID Partition Map`.
4. Re‑run balenaEtcher:
   - Optionally disable **validation** in Etcher’s settings (gear icon in the UI) to avoid verify‑stage hangs.
5. If problems persist:
   - Try a different USB stick.
   - Re‑download the Ubuntu ISO.

***

### 2.5 Download Ubuntu ISO

1. On windows machine with a browser, download the latest LTS 64‑bit Ubuntu Desktop ISO from the official Ubuntu download page and save it locally (for example to `Downloads`).  
2. Copy or make sure this ISO is available on the Windows PC that will run Rufus.

### 2.6 Create the USB installer on Windows with Rufus

1. On a Windows PC, plug in an empty USB flash drive (8 GB or larger).  
2. Download and run Rufus (portable executable is fine).  
3. In Rufus:  
   - **Device**: select the target USB drive.  
   - **Boot selection**: choose the downloaded Ubuntu ISO.  
   - **Partition scheme**: use GPT, **Target system**: UEFI (recommended for HP t540).  
   - Leave file system and cluster size at defaults.  
4. Click **Start**, confirm that the USB will be erased, and wait until Rufus reaches 100% and reports completion.  
5. Safely eject the USB drive from Windows and move it to the HP t540 to continue with the installation steps already documented.

***

## 3. Booting the HP t540 from the Ubuntu USB

### 3.1 Entering the HP boot menu

1. Connect the Ubuntu USB installer to the HP t540.
2. Power on the t540 and immediately tap `Esc` repeatedly.
3. At the HP Startup Menu:
   - Press `F9` for **Boot Device Options**.
   - Select the USB drive (it may show by vendor name or as “USB HDD”) and press Enter.

### 3.2 Adjusting BIOS/UEFI if USB does not appear

1. From the Startup Menu, press `F10` for **BIOS Setup**.
2. In BIOS:
   - Ensure **USB boot** is enabled.
   - If Secure Boot is enabled and Ubuntu USB will not boot, disable **Secure Boot** and enable **Legacy/CSM** or allow third‑party bootloaders (exact labels depend on firmware).
3. Save changes (usually `F10` → **Yes**) and let the system reboot.
4. Tap `Esc` again → `F9` and select the USB device.

***

## 4. Installing Ubuntu on the HP t540

1. When the Ubuntu boot menu appears, choose **Try or Install Ubuntu**.
2. After the live desktop loads, double‑click **Install Ubuntu**.
3. Follow the installer:
   - Select language and keyboard layout.
   - Choose **Normal installation**.
   - Optionally enable “Download updates while installing” and third‑party codecs.
4. Disk setup:
   - For a clean homelab node, choose **Erase disk and install Ubuntu** to dedicate the internal SSD to Ubuntu.
5. Set:
   - Time zone.
   - User name, computer name, and password.
6. Start the installation and wait until it completes.
7. When prompted, restart and remove the USB.
8. Confirm the system boots directly into the new Ubuntu installation.

***

## 5. Dealing with “No Wi‑Fi Adapter Found” on Ubuntu (HP t540)

After install, Ubuntu’s Wi‑Fi settings may show **“No Wi‑Fi Adapter Found”**:

1. Understand that many HP t540 thin clients ship **without** a built‑in Wi‑Fi card; only Ethernet is present.
2. Verify hardware:
   - Connect the t540 to the network via Ethernet.
   - Open a terminal and run:
     ```bash
     lshw -C network
     lspci | grep -i network
     ```
   - If only an Ethernet interface appears (no wlan device), there is no internal Wi‑Fi radio.
3. Approach:
   - Continue using wired Ethernet for homelab use, **or**
   - Add an external USB Wi‑Fi adapter known to work well with Linux (see next section).

If a wireless device is present but no driver is loaded, use Ubuntu’s driver tools or install the appropriate driver package. For a homelab thin client, Ethernet is usually preferred for stability and throughput.

***

## 6. Choosing a Linux‑friendly USB Wi‑Fi adapter

When adding Wi‑Fi to Ubuntu on the t540:

- Prefer adapters with chipsets supported **in the mainline Linux kernel** and explicitly advertised as Linux‑compatible.
- Avoid generic, unbranded “150 Mbps 802.11n” dongles whose chipsets change between batches.

Example adapter characteristics to look for:

1. Clearly specified model and chipset (e.g., certain Atheros/Intel/MediaTek parts).
2. Product page or documentation that states “works with Linux/Ubuntu”.
3. Presence on curated “Linux‑supported Wi‑Fi adapter” lists.

***

## 7. Homelab equipment cost

This section summarizes the example hardware cost for a small homelab based on two HP t540‑class nodes and supporting accessories.

### 7.1 Itemized costs

- **Servers** – two thin clients (each 8 GB RAM, 128 GB SSD): **118.70**  
- **Power cables** – for both machines: **22.51**  
- **USB Wi‑Fi dongle** – Linux‑compatible adapter: **25.00**

### 7.2 Total cost

- **Total homelab hardware cost**:  
  118.70 (servers)  
  + 22.51 (power cables)  
  + 25.00 (Wi‑Fi dongle)  
  = **166.21**

***

## 8. Next steps for the homelab

Once Ubuntu is running on the t540 nodes:

1. Update the system:
   ```bash
   sudo apt update && sudo apt upgrade
   ```
2. Install homelab basics:
   ```bash
   sudo apt install openssh-server git tmux
   ```
3. Decide homelab role per node:
   - Kubernetes or k3s cluster nodes.
   - Docker container hosts.
   - Lightweight services (DNS, DHCP, monitoring, reverse proxy, etc.).
4. Document IP addresses, hostnames, and roles for each machine in this same `homelab/readme.md` file as the lab grows.
