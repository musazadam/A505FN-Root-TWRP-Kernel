# A505FN: Persistent TWRP + Magisk Root + Android Mint Beta Kernel

This guide documents how I got **persistent TWRP, full Magisk root, and the Android Mint Beta kernel** working on the **Samsung Galaxy A50 (SM-A505FN)**.

**Tested on: SM-A505FN · A505FNXXS9CVJ2 · [TWRP v3.7.1] · [Mint Kernel Beta 1413] · [Magisk v30.7]**

> ⚠️ **IMPORTANT**
>
> This is specifically for the **SM-A505FN**. Do not blindly flash these files on another A50 variant.
>
> You are responsible for your device. Keep the complete stock firmware available so you can recover with Odin if something goes wrong.
>
> Make sure you understand how to enter **Download Mode and Recovery Mode before starting**.
>
> Make sure to back up any important data before proceeding as you will have to format your data

## Disclaimer

This guide is provided for informational purposes only. Flashing firmware, custom recoveries, kernels, and root modifications can potentially cause data loss, bootloops, void your warranty, trip knox, or damage your device.
**Use this guide at your own risk. I am not responsible for any damage, data loss, or other consequences resulting from following this guide.**

**This guide is licensed under CC BY 4.0. The third-party software, firmware, kernels, and tools referenced here remain under their respective licenses.**

Make sure you understand each step and have a way to restore the device to stock firmware before proceeding.


## What you will end up with

* Persistent TWRP recovery
* Magisk root
* Android Mint Beta custom kernel
* Root, TWRP, and the kernel surviving normal reboots

## References and Credits:

- vbmeta-disable-verification — WessellUrdata
https://github.com/WessellUrdata/vbmeta-disable-verification
- TWRP — Team Win Recovery Project
https://twrp.me/samsung/samsunggalaxya505f.html
- Magisk — topjohnwu
https://github.com/topjohnwu/Magisk
- Mint Kernel - FreshROMs
https://github.com/FreshROMs/android_kernel_samsung_exynos9610_mint/releases (
MintBeta-xxxx.A11.OneUI3-Permissive_A50.CI.zip or MintBeta-xxxx.A11.OneUI3-Enforcing_A50.CI.zip - recommended)
- Multi-Disabler — Eureka
https://yer.dl.sourceforge.net/project/eurekaroms/web_files/multidisabler-samsung-3.1.zip
- Samsung Firmware - SAMFW
https://samfw.com/firmware/SM-A505FN (search for A505FNXXS9CVJ2 in 'search firmware')

You can do it with any Operating System, personally I used Arch Linux.

---

## 1. Install the correct stock firmware

Start from:

**A505FNXXS9CVJ2**

Install the stock firmware normally and boot into Android.

Before continuing:

* Confirm the phone is **SM-A505FN**
* Make sure the bootloader is unlocked
* Make sure **OEM unlocking** is enabled/unlocked
* Make sure the stock firmware installation completed successfully
* Make sure you know the key combinations for:

  * Recovery Mode
  * Download Mode

Do not continue if the phone is already behaving strangely or the stock installation is incomplete.

If OEM Unlocking does not appear after installing this firmware, connect to Wi-Fi and check for system updates. On some Samsung firmware versions, changing the device date to an earlier date and checking Software Update can cause the OEM Unlocking option to reappear. This behavior is firmware-dependent and is not guaranteed to work. If OEM Unlocking appears greyed out while the bootloader is already unlocked, verify its actual state before continuing. **Do not update.**

---

## 2. Patch vbmeta

Download the vbmeta-disable-verification tool. Extract the AP file from the firmware you downloaded, after that you will see zipped vbmeta file, extract it too, use the provided script to patch/disable verification.

The goal is to produce a patched **vbmeta.img** image.

Then zip it with this command:

```bash
tar --format=ustar -cvf vbmeta_disabled.tar vbmeta.img
```

### Why are we doing this?

Samsung's verified-boot system can prevent modified partitions such as TWRP or a custom kernel from booting.

The patched vbmeta disables Android Verified Boot verification for the relevant partitions, allowing the modified recovery/kernel setup to boot.

---

## 3. Flash the patched vbmeta

Place the vbmeta_disabled.tar into the **AP** slot in Odin.

Flash it.

### VERY IMPORTANT

**Do NOT allow the phone to boot normally after this.**

Immediately return to **Download Mode**.

The reason is that we want to continue modifying the boot/recovery environment before Android gets a chance to boot normally.

---

## 4. Flash TWRP

Put the TWRP image into Odin's **AP** slot.

Flash it.

After flashing, boot directly into **TWRP Recovery**.

For this setup, keep the **USB cable connected** while entering recovery.

If Android boots instead, don't panic — return to Download/Recovery Mode and repeat the correct boot combination.

---

## 5. Format Data

Once inside TWRP:

**Wipe → Format Data**

This is **Format Data**, NOT the normal Factory Reset/Wipe Data operation.

Confirm the format.

This is important because encryption/data state can otherwise interfere with the modified recovery/kernel setup.

> ⚠️ Formatting Data erases the user's data on the device. Back up anything important beforehand.

---

## 6. Sideload Multi-Disabler

From TWRP, use:

**Advanced → ADB Sideload**

Then sideload the compatible **Multi-Disabler** ZIP for the device/firmware.

Wait for it to finish successfully.

Afterward, reboot back into **Recovery**, not directly into Android.

---

## 7. Install Android Mint Beta Kernel

Once TWRP has restarted:

Use TWRP's ADB Sideload function again and sideload the **Android Mint Beta kernel for One UI**.

For this setup:

* Use the **Magisk-compatible/rooting route**
* Do **not** use the KSU version
* **SELinux Enforcing is recommended**
* Permissive mode can be done, but it is less desirable from a security perspective

Wait for the kernel installation to finish successfully.

---

## 8. Wipe cache and Dalvik

In TWRP:

**Wipe → Advanced Wipe**

Select:

* Cache
* Dalvik / ART Cache

Wipe them.

Then reboot into Android, booting can take up some time and it is expected for first boot.

---

## 9. Complete Android setup

Boot into Android and complete the initial setup.

Install the **Magisk APK**.

### Keep the Magisk APK!

You will need it again from TWRP.

At this point, opening Magisk should show that the device has a ramdisk.

Reboot into **TWRP Recovery** again.

---

## 10. Install Magisk through TWRP

In TWRP:

**Install → select the Magisk APK**

TWRP should treat the APK as an installable package.

Flash it.

Then reboot into Android.

---

## 11. Finish Magisk setup

Open Magisk.

It should ask to perform the additional installation/setup step.

Allow it to perform the **Direct Install**.

Reboot when requested.

After rebooting, Magisk should report that root is installed.

---

# Done 🎉

At this point you should have:

A fully persistent and rooted A505FN with custom kernel and TWRP.

You can now use the rooted system normally.

## Recovery plan

Before experimenting with modifications, keep:

* Your exact stock **A505FNXXS9CVJ2** firmware
* Odin
* The working TWRP image
* The working vbmeta image
* A known-good Multi-Disabler
* The Mint kernel package

If something goes wrong, the safest recovery method is generally to return to the known-good stock firmware rather than randomly flashing partitions until something works.

## Final warning

Don't assume that because this worked on an **A505FN with A505FNXXS9CVJ2**, it will work on every A50 firmware/version.

Bootloader state, firmware revision, kernel compatibility, recovery compatibility, and partition layout matter.

**Verify every file before flashing it.**
