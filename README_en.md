# Ryzentosh - MSI X670E Opencore EFI

[**English**](README_en.md)| [**中文**](README.md)

## Hardware Configuration
| **Component** | **Model** |
| ------------- | --------- |
| CPU | AMD Ryzen 7 9700X @ 3.6GHz |
| Motherboard | MSI X670E GAMING WIFI |
| RAM | Gloway Dragon DDR5 32GB (2 x 16GB) @5600MHz |
| Audio | Realtek ALC-897 |
| GPU | XFX RX 6950 XT 16G |
| Ethernet | Realtek RTL8125B 2.5GbE |
| WiFi & Bluetooth | Broadcom BMC94360CD |
| PSU | Seasonic Vertex PX-1000 1000W |
| OS Drive | Kioxia SSD 500G |

**macOS Version**: 13.2 (22D49)

**OpenCore Version**: 1.0.5

**SMBIOS**: MacPro7,1

## Table of Contents
- [Changelog](#changelog)
- [Drivers and Kexts](#drivers-and-kexts)
- [How to Use](#how-to-use)
- [BIOS Settings](#bios-settings)
- [Compatibility](#compatibility)
- [Sleep Information](#sleep-information)
- [PAT Patch Information](#pat-patch-information)
- [Adobe Software Fix](#adobe-software-fix)
- [Virtualization](#virtualization)
- [Installation Guides](#installation-guides)

## Changelog
- 2025-10-23
1.  Customized USB via ACPI, generated SSDT-XHUB.aml, deprecated USBMap.kext.
2.  Used modified DSDT: DSDT-fix1107.aml, removed all new BIOS conditional statements, successfully loaded onboard Ethernet. (Used ACPI debug to obtain values for variables like G000, G001, G002, calculated whether the condition held true, and then decided to either remove the entire code block or just the conditional check.)
3.  Used SSDT-Basic-AM5.aml universal patch, includes CPUR, USBX, RTC, EC0, disables unsupported iGPU (I disabled it directly in BIOS), USB fixes, etc.
4.  Customized AppleALC.kext, version 1.9.5. Achieved separation of speakers and headphone jack.
5.  Enabled MMIOWHiteList for native NVRAM.
6.  Added `acpi-wake-type | Data | 01` to all USB controllers to improve the issue where mouse/keyboard wake requires two attempts. Current testing shows limited effect.
7.  Added "Disable RTC wake scheduling" patch to resolve periodic wake issues.

## Drivers and Kexts
- [[Bootloader] OpenCore](https://github.com/acidanthera/OpenCorePkg)
- [[Resources] Picker GUI](https://github.com/acidanthera/OcBinaryData/tree/master/Resources)
- [[Patch] AMD_Vanilla](https://github.com/AMD-OSX/AMD_Vanilla)
- [[Driver] FwRuntimeServices](https://github.com/acidanthera/OpenCorePkg)
- [[Driver] HfsPlus](https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlus.efi)
- [[Driver] OpenHfsPlus](https://github.com/acidanthera/OpenCorePkg)
- [[Driver] OpenRuntime](https://github.com/acidanthera/OpenCorePkg)
- [[Driver] OpenCanopy](https://github.com/acidanthera/OpenCorePkg)
- [[Kext] Lilu](https://github.com/acidanthera/Lilu)
- [[Kext] VirtualSMC](https://github.com/acidanthera/VirtualSMC)
- [[Kext] WhateverGreen](https://github.com/acidanthera/WhateverGreen)
- [[Kext] AppleALC Audio Driver](https://github.com/acidanthera/AppleALC)
- [[Kext] AppleMCEReporterDisabler Disable AppleMCERReport](https://github.com/AMD-OSX/AMD_Vanilla/raw/master/Extra/AppleMCEReporterDisabler.kext.zip)
- [[Kext] LucyRTL8125Ethernet 2.5G Ethernet Driver](https://github.com/Mieze/LucyRTL8125Ethernet)
- [[Kext] AMDRyzenCPUPowerManagement](https://github.com/trulyspinach/SMCAMDProcessor)
- [[Kext] SMCProcessorAMD](https://github.com/macos86/SMCProcessorAMD)
- [[Kext] NVMeFix](https://github.com/acidanthera/NVMeFix)
- [[Kext] RestrictEvents](https://github.com/acidanthera/RestrictEvents)
- [[Kext] Innie](https://github.com/cdf/Innie/releases)
- [[SSDT] EC-USBX-DESKTOP](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-EC-USBX-DESKTOP.aml)
- [[Tool] GenSMBIOS](https://github.com/corpnewt/GenSMBIOS)
- [[Tool] OpenCore Online Editor](https://galada.gitee.io/opencoreconfiguratoronline/)
- [[Tool] PlistEdit Pro (Mac)](https://www.macwk.com/soft/plistedit)
- [[Tool] Hackintool](https://github.com/headkaze/Hackintool)
- [[Tool] OpenCore Configurator (Mac)](https://www.macwk.com/soft/opencore-configurator)
- [[Tool] gibmacOS](https://github.com/corpnewt/gibMacOS)
- [[Tool] MaciASL](https://github.com/acidanthera/MaciASL)
- [[Tool] OCConfigCompare](https://github.com/corpnewt/OCConfigCompare)

## How to Use
  1.  Use [**this guide**](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/) to create a USB installer.
  2.  Clone this repository and copy the "BOOT" and "OC" directories into the "EFI" folder on your boot drive.
  3.  Download [**GenSMBIOS**](https://github.com/corpnewt/GenSMBIOS) to generate unique SMBIOS information. Run it, select "Generate SMBIOS", and choose `iMacPro1,1` as the model.
  4.  Open `config.plist` using [**ProperTree**](https://github.com/corpnewt/ProperTree). Navigate to `PlatformInfo > Generic`. Set `MLB` (Main Logic Board Serial), `SystemSerialNumber` (Serial), and `SystemUUID` (SmUUID) to the generated values. Change `ROM` to your network card's MAC address without colons. [**How to find MAC address?**](https://www.wikihow.com/Find-the-MAC-Address-of-Your-Computer)
  5.  Update your BIOS to the latest version and set the BIOS settings to the [**required values**](#bios-settings).
  6.  Read the [**information**](#compatibility) about required changes for specific hardware.
  7.  Boot and install macOS!
  8.  After installation, you can copy the EFI directory to the EFI partition of your disk - then you can boot macOS without the USB drive.
  9.  If dual-booting, you must enable `Bootstrap` to protect your OpenCore from being overwritten by Windows bootloader. Click for more [**information**](https://dortania.github.io/OpenCore-Post-Install/multiboot/bootstrap.html).

  If it doesn't boot correctly, check:
  1.  In `Kernel -> Quirks`, is `ProvideCurrentCpuInfo` set to `True`? (Must be enabled, otherwise it won't boot).
  2.  In `Misc -> Security -> SecureBootModel`, is it set to `Disabled`?
  3.  In `Kernel -> Patch`, has the `algrey - Force cpuid_cores_per_package` patch been modified according to your CPU core count? See [AMD_Vanilla](https://github.com/AMD-OSX/AMD_Vanilla) for details.

**The SMBIOS information in this repository has been removed. You must generate and add your own unique SMBIOS. [Generator Tool](https://github.com/corpnewt/GenSMBIOS)**

## BIOS Settings

| **Option** | **Status** |
| ------------- | --------- |
| SATA Mode | AHCI |
| Above 4G Decoding | Enabled  |
| EHCI/XHCI Hand-off | Enabled |
| SVM | Enabled |
| CSM | Disabled |
| Resizable BAR Support | Disabled |
| Secure Boot | Disabled |
| Serial Port | Disabled |
| Parallel Port | Disabled |
| TPM Device | Disabled <sup>2</sup>|

<sup>1</sup> Disable TPM during the installation phase. It can be enabled after installation, especially for Windows 11 dual-boot, as Windows 11 won't boot without TPM enabled.

**Many of these options might not exist in your motherboard's BIOS options, just get as close as possible. Don't worry too much if many of these options aren't in your BIOS.**

**Remember to update your BIOS to the latest version before booting macOS**

## Compatibility
Works with most AMD CPUs with 17h and 19h families, all Ryzen series and Athlon 2xxGE.
Does NOT work with 15h (FX series), 16h (A series), and Threadripper CPUs.

See [**Support List**](https://dortania.github.io/Anti-Hackintosh-Buyers-Guide/CPU.html) for details.

**Integrated Graphics do not work properly**. For NVIDIA GPUs, see [**here**](https://dortania.github.io/GPU-Buyers-Guide/modern-gpus/nvidia-gpu.html).

If you are an NVIDIA user, you may need to select the Algrey version of the PAT patch, see [**here**](#pat-patch-information) for details.

**B550 and A520 motherboards** need to add the **SSDT-CPUR** ACPI patch to boot normally. [**Download**](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-CPUR.aml) \
Download and place it in the `OC/ACPI` directory, then enable it in the `config.plist`.

**B550, A520 motherboards, and B450, X470, X570 motherboards using the latest BIOS versions** must disable `SetupVirtualMap` in the config. The path is `Booter -> Quirks -> SetupVirtualMap`, set the value to `false`.

**AMD Navi GPUs (e.g., RX 5500, 5600, 5700)** should add `agdpmod=pikera` to the `boot-args` to fix black screen issues.

If you have audio issues, you must change the `alcid` value to match your motherboard. See [**here**](https://github.com/acidanthera/applealc/wiki/supported-codecs). You can try different layout-id values until your audio works correctly.

If you have network connection issues, it might be that the Ethernet kext is not suitable. Please refer to the manual to find the Ethernet driver for your motherboard. [**See here**](https://dortania.github.io/OpenCore-Install-Guide/ktext.html#ethernet).

The Shaneee version of the PAT patch is enabled by default, which can provide better GPU performance but may also cause some compatibility issues. If you have compatibility problems, please switch to the other PAT patch. For details, see [**PAT Patch Information**](#pat-patch-information).

## Sleep Information

If you have sleep issues, first customize your USB ports. Customization method [**refer here**](https://dortania.github.io/OpenCore-Post-Install/usb/). If customizing USB still doesn't resolve the issue, you should try fixing USB via SSDT.

There are patches in SSDT-SLEEP.aml for patching the `_STA` method. The patches are applied to the `_SB.PCI0.GPP2.PTXH` and `_SB.PCI0.GP17.XHC0` USB controllers. If your USB controllers have other addresses, you must add them to the SSDT. The patch only affects macOS, so USB on other systems is unaffected.

Sleep issues are often caused by USB, but not always. If the USB patch doesn't work, refer to [**this article**](https://dortania.github.io/OpenCore-Post-Install/universal/sleep.html) to fix sleep.

## PAT Patch Information
| **Shaneee's** | **Algrey's** |
| ------------- | --------- |
| Better GPU Performance | Worse GPU Performance |
| May not work with NVIDIA GPUs | Compatible with all GPUs |
| HDMI / DP Audio may not work | HDMI/DP Audio works correctly |
| Enabled by default | Disabled by default |

To switch to the other patch, search for `mtrr_update_action` in `config.plist`. Then set `Enable` to `TRUE` for the patch you want to use and `FALSE` for the other.

Do not try to use both patches simultaneously; one must be disabled.

## Adobe Software Fix

Due to the missing `intel_fast_memset` instruction, Adobe applications crash on AMD Hackintoshes. You can run [**this script**](/Resources/Adobe%20patch.sh) to fix it, or follow [**this tutorial**](https://gist.github.com/mikigal/8e1f804fcd7dbafbded2f236653be7c8) to fix it manually! Remember to restart the system after patching.

If Photoshop crashes when opening images from a file, you must downgrade it to version 22.0.

## Virtualization

First, you must enable `SVM` in the BIOS settings.
Parallels Desktop (only version 13.1, newer versions require AppleHV) and VirtualBox (which works much worse than Parallels) are supported.
VMWare Fusion 10 does not work on Big Sur. On Catalina, it requires this [**workaround**](https://posts.boy.sh/vmware-fusion-catalina). \
Docker also doesn't work properly. You must use Docker Toolbox, but it doesn't have all the features of Docker.

On Big Sur (11.0), Parallels fails to start normally and prompts `Required components are missing from the OS` error. You must add the `SYSTEM_VERSION_COMPAT=1` parameter during installation. \
Run the following command in the terminal: `SYSTEM_VERSION_COMPAT=1 open /Volumes/Parallels\ Desktop\ 13.1.0/Install.app/`, replace the path with the location of the Parallels installer file. \

The same error occurs when starting Parallels after installation. You can either add the same parameter every time you start it, or use a launcher made by experts. [**Download**](/Resources/Parallels%20Desktop%20Launcher.app.zip)

Parallels 13.1 only supports Windows 10 version 1607 and older. Newer versions get stuck during installation. Using Windows 7 is recommended for better performance. Do not use the automatic installation feature.

Do not assign too many resources to the VM, as it can cause performance issues.
I tested many VM configurations - the best performance results were as follows:

Parallels Desktop 13.1
4 CPU Cores
4GB RAM
1GB VRAM
3D Acceleration: DirectX 9
OS: Windows 7 (SP1, Build 7601) with Aero themes disabled.

If the VM OS cannot see USB devices, try reconnecting them to a different port; this usually solves the problem.
If [**Coherence Mode**](https://www.parallels.com/blogs/how-to-use-coherence-mode-in-parallels-desktop/) doesn't work, you must disable the VM's antivirus software or add the following files to its exclusion list:

  - `C:\Program Files (x86)\Parallels\Parallels Tools\Services\coherence.exe`
  - `C:\Program Files (x86)\Parallels\Parallels Tools\Services\prl_hook.dll`

## Installation Guides
**If you encounter any issues during installation or booting macOS, kernel panics, or other system-related problems, please check the OC Configuration Guide**

**If other things aren't working correctly (e.g., USB ports, iServices, DRM/Netflix), please check the Post-Install Guide**

- Creating a USB Installer: [**\*Click\***](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/)
- OpenCore Configuration: [**\*Click\***](https://dortania.github.io/OpenCore-Install-Guide/AMD/zen.html)
- Post-Installation: [**\*Click\***](https://dortania.github.io/OpenCore-Post-Install/)
- Troubleshooting: [**\*Click\***](https://dortania.github.io/OpenCore-Post-Install/)
- ACPI Patching: [**\*Click\***](https://dortania.github.io/Getting-Started-With-ACPI/)
- USB Mapping: [**\*Click\***](https://dortania.github.io/OpenCore-Post-Install/usb/)
