# Windows USB Recovery Toolkit

One-drive, dual-partition USB toolkit that pairs a bootable **Hiren's BootCD PE x64** with a large **exFAT tools partition** for fast incident triage and Windows recovery.

## Problem / Objectives

- Build a dependable, portable recovery USB that:

  - Boots into **Windows 10 PE (Hiren's BootCD PE x64)** for offline troubleshooting.

  - Includes a separate **exFAT** partition for drivers, logs, utilities, and exports.

- Prove access and recovery capability (local account reset test), and add a fast-access **AdminTools ("God Mode")** launcher.

## Environment & Prerequisites

- **Host OS:** Windows 11/10 (Admin rights).

- **Media:** One USB 3.0 flash drive, **≥64 GB** (128 GB preferred).

  - USB-A or USB-C; UEFI-bootable firmware recommended.

- **Downloads:**

  - `HBCD_PE_x64.iso` (Hiren's BootCD PE x64 v1.0.2; ~2.9 GB).

  - `ISO2USB.exe` (Hiren's official PE writer tool).

> Tip: Verify ISO integrity before writing by comparing SHA-256 hashes from the official site.

## Steps Summary

1. Download `HBCD_PE_x64.iso` and `ISO2USB.exe` from the official Hiren's site.

2. Insert the target USB (≥64 GB) and back up any data on it.

3. Launch **ISO2USB.exe**, select the USB device and the `HBCD_PE_x64.iso`, enable **Format** and write the image.

4. Open **Disk Management** and **shrink** the bootable partition if it consumed the whole drive.

5. Create a second **exFAT** partition (label: `exFatUSB`) for tools, logs, drivers, and exports.

6. Optional hash check:

   ```powershell
   certutil -hashfile .\HBCD_PE_x64.iso SHA256
   ```

7. Reboot and use the firmware **one-time boot menu** to boot from the USB (UEFI preferred).

8. In Hiren's PE, verify access to core tools (disk, imaging, AV, password, Sysinternals, network).

9. **Local account reset drill:** create a temporary local admin `HirenTest`, then use Hiren's **Security → Passwords** utility to change/remove its password offline.

10. Create a **God Mode** folder on `exFatUSB` for quick admin access:

    ```
    AdminTools.{ED7BA470-8E54-465E-825C-99712043E01C}
    ```

11. Validate results and document evidence (descriptions below).

12. Note limitations: domain-joined accounts require domain/IDP workflows; local tools affect local accounts only.

## Evidence & Artifacts (descriptions only — no image files required)

* **Artifact 01 — Hiren's download page:** Official page showing `HBCD_PE_x64.iso` name, file size, and published hashes.

  **Alt text:** "Download table listing HBCD_PE_x64.iso with MD5/SHA-1/SHA-256 values and ~2.9 GB size."

* **Artifact 02 — ISO2USB configuration:** Writer tool window with USB target selected, ISO path set, and Format option enabled.

  **Alt text:** "ISO2USB shows selected removable drive, ISO path, and a checked 'Format' box."

* **Artifact 03 — ISO2USB completion:** Confirmation dialog indicating the boot sector and files were written successfully.

  **Alt text:** "Progress bar complete with a success status message."

* **Artifact 04 — Partition layout:** Windows Disk Management illustrating a small bootable partition and a large **exFAT** partition labeled `exFatUSB`.

  **Alt text:** "Removable disk with two partitions; one active/EFI, one exFAT data volume."

* **Artifact 05 — UEFI boot menu:** Firmware boot picker with the USB device selected under UEFI entries.

  **Alt text:** "One-time boot menu showing 'UEFI: <USB Brand/Model>' highlighted."

* **Artifact 06 — Hiren's PE desktop:** PE environment open with categories such as Hard Disk, Security, System Tools, Network.

  **Alt text:** "Windows PE desktop wallpaper and a menu of troubleshooting tools."

* **Artifact 07 — Password tool result:** Hiren's password utility showing the `HirenTest` local account password changed/cleared with a success log.

  **Alt text:** "Task/result pane confirming local user password operation completed."

* **Artifact 08 — Post-reset sign-in:** Windows sign-in screen confirming access to `HirenTest` after the reset.

  **Alt text:** "Login accepted for local account; desktop loads successfully."

* **Artifact 09 — God Mode folder view:** Explorer showing `AdminTools.{ED7BA470-…}` expanding into a unified list of administrative controls.

  **Alt text:** "Control Panel categories consolidated in a single folder-style view."

## Results & Validation

* USB boots into **Hiren's PE** via UEFI on test hardware.

* Local account `HirenTest` password operation succeeds and interactive logon confirms access.

* Secondary **exFAT** partition is readable/writeable in Windows and contains the **AdminTools (God Mode)** folder.

## What I Learned

* Authoring a **UEFI-bootable Windows PE** recovery drive and keeping a **separate tools partition** for workflow speed.

* Hands-on use of **offline security, disk, and imaging** utilities for incident response.

* Limits of offline password tools with **domain-joined** devices vs. local accounts.

* Faster navigation of Windows administration via **God Mode**.

## Troubleshooting Notes

* **USB won't boot:** Ensure **UEFI** is enabled; toggle **Secure Boot** off if needed; try another port; re-write with ISO2USB.

* **USB not listed in ISO2USB:** Pre-format the drive in Windows (Restore defaults) and press **Refresh** in ISO2USB.

* **Can't create second partition:** Shrink the first partition in **Disk Management**; if blocked, back up and fully re-image the USB, then partition.

* **Password tool had no effect:** Confirm you targeted a **local** account; domain accounts require domain/IDP reset procedures.

* **Hash mismatch:** Re-download the ISO and verify again before writing.
