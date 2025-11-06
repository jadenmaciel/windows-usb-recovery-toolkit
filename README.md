# Windows USB Recovery Toolkit

One-drive, dual-partition USB toolkit that pairs a bootable **Hiren's BootCD PE x64** with a large **exFAT tools partition** for fast incident triage and Windows recovery.

## Problem / Objectives

- Build a dependable, portable recovery USB that:

  - Boots into **Windows 10 PE (Hiren's BootCD PE x64)** for offline troubleshooting.

  - Includes a separate **exFAT** partition for drivers, logs, utilities, and exports.

- Prove access and recovery capability (local account reset test), and add a fast-access **AdminTools ("God Mode")** launcher. :contentReference[oaicite:25]{index=25}

## Environment & Prerequisites

- **Host OS:** Windows 11/10 (Admin rights).

- **Media:** Two USB 3.0 flash drives, **≥64 GB** each (128 GB preferred).

  - USB-A or USB-C; UEFI-bootable firmware recommended.

- **Downloads:**

  - `HBCD_PE_x64.iso` (Hiren's BootCD PE x64 v1.0.2; ~2.88 GB). Verify with vendor hashes when possible (MD5/SHA-1/SHA-256 published by Hiren's). :contentReference[oaicite:26]{index=26}

  - **ISO2USB.exe** (Hiren's official PE writer tool). :contentReference[oaicite:27]{index=27}

> ⚠️ Avoid adware "download" buttons; use the official Hiren's links only. :contentReference[oaicite:28]{index=28}

## Steps Summary

1. **Stage ISO** on **USB-1**: download `HBCD_PE_x64.iso` and `ISO2USB.exe`. :contentReference[oaicite:29]{index=29}  

2. **Prepare USB-2 (target):** ensure it's ≥64 GB and UEFI-bootable.

3. **Write Hiren's to USB-2** with **ISO2USB.exe**: choose the device, select ISO, optionally set volume label (≤11 chars), then **format + copy**. :contentReference[oaicite:30]{index=30}

4. **Create a second partition** on USB-2 for tools and label it `exFatUSB` (exFAT; ≥32 GB). (If ISO2USB takes the whole disk, shrink the Hiren's partition then create exFAT.)

5. **Name the bootable partition** `HirenBPE` (or similar; format is managed by the writer). :contentReference[oaicite:31]{index=31}

6. **(Optional) Verify ISO integrity** before writing:

   ```powershell
   certutil -hashfile .\HBCD_PE_x64.iso SHA256
   ```

Compare to the vendor's SHA-256. 

7. **Boot test:** reboot to the USB from your firmware's **Boot Menu** (UEFI preferred).

8. **In Hiren's PE**, explore tools (disk, imaging, AV, password, Sysinternals, network). 

9. **Local account reset drill:** on a Windows 10 machine, create a throwaway admin account `HirenTest` with password `1qaz!QAZ1qaz`, then demonstrate offline password change/remove using Hiren's **Security → Passwords** tools. Capture **Task/Result** details. 

10. **Create "God Mode" folder** on `exFatUSB`:

- New folder named exactly:

`       AdminTools.{ED7BA470-8E54-465E-825C-99712043E01C}

      `

This exposes a consolidated view of administrative controls. 

11. **Validate access & artifacts** (see list below) and document outcomes.

12. **(Notes)** Domain-joined devices won't honor local-only resets for domain accounts—understand scope/limitations. 

## Evidence & Artifacts

> Name screenshots exactly; you can capture/upload later into `/artifacts/`.

* `01-hirens-site-iso-page.png` — *Official Hiren's BootCD PE download page with ISO name/size and hash values visible.* **Alt:** "HBCD_PE_x64.iso listed with MD5/SHA-1/SHA-256 and 2.88 GB file size." 

* `02-iso2usb-device-and-iso.png` — *ISO2USB.exe with target USB selected and ISO path populated.* **Alt:** "USB device drop-down; HBCD_PE_x64.iso selected; Format option checked." 

* `03-iso2usb-complete.png` — *ISO2USB success dialog indicating files copied and boot sector prepared.* **Alt:** "Progress finished; success status." 

* `04-disk-management-partitions.png` — *Windows Disk Management showing `HirenBPE` boot partition and large `exFatUSB` partition.* **Alt:** "UEFI bootable partition + exFAT data partition."

* `05-uefi-boot-menu.png` — *Firmware one-time boot menu with the USB device chosen.* **Alt:** "UEFI: <USB brand/model> selected."

* `06-hirens-pe-desktop.png` — *Hiren's BootCD PE desktop with Tools menu visible.* **Alt:** "PE desktop; tool groups like Hard Disk, Security, System Tools, Network." 

* `07-password-tool-task-details.png` — *Password reset utility showing `HirenTest` account changed/cleared.* **Alt:** "'Task details' or result log indicating success for local user." 

* `08-win10-sign-in-after-reset.png` — *Windows sign-in confirming access to `HirenTest` post-reset.* **Alt:** "Local login screen success state."

* `09-admintools-godmode-folder.png` — *Explorer view of `AdminTools.{ED7BA470-…}` folder displaying the unified admin settings list.* **Alt:** "Control panel categories combined in one view." 

## Results & Validation

* USB boots into **Hiren's PE** on UEFI hardware.

* Password tool **modifies a local account** (`HirenTest`) and login succeeds afterward (screenshot of tool "Task details" + Windows sign-in).

* `exFatUSB` is readable/writeable on Windows and holds the **AdminTools (God Mode)** folder view.

* Understanding clarified: **domain accounts** on managed machines are **not** resettable via local offline tools; domain policy controls apply. 

## What I Learned

* How to **author a UEFI-bootable Windows PE** recovery drive and keep a **separate tools partition**.

* Practical use of **Hiren's security, disk, and imaging utilities** during offline response. 

* Differences between **local vs. domain credential** reset scenarios and why domain joins block local-only resets. 

* Fast navigation via **AdminTools (God Mode)** for consolidated Windows administration. 

## Troubleshooting Notes

* **Won't boot USB:** Ensure **UEFI** is enabled; disable **Secure Boot** if required; try a different port; re-write the USB with ISO2USB.

* **USB not listed in ISO2USB:** Pre-format with Windows Format (restore defaults), then click **Refresh** in ISO2USB. 

* **Partition layout issues:** If ISO writer consumes full disk, **shrink** the PE partition then create the exFAT partition; as a last resort, back up and rebuild.

* **Password tool "no effect":** Verify you're targeting a **local** account; domain-joined endpoints require domain workflows (helpdesk/IDP reset). 

* **Corrupt ISO:** Re-download and verify hash (MD5/SHA-256) against the published values. 
