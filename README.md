# Asus Vivobook X510UA-BQ490

Tested on 10.14.4-10.15 (Clover) and 10.15 Beta 2 (OpenCore) 

![Alt text](https://ivanov-audio.com/wp-content/uploads/2014/01/Hackintosh-Featured-Image.png)

## System specification

1. Name:           Asus Vivobook X510UA-BQ490
2. CPU:            Intel Core i5-8250U
3. Graphics:        Intel UHD Graphics 620 // Dual monitor with HDMI (Index 2) and 2048 MB VRAM
4. Wifi:           Intel Dual Band Wireless-AC 8265 - with bluetooth // REPLACED WITH DW1560 (AirDrop and Handoff Working perfectly)
5. Card Reader:    Connected via USB
6. Camera:         ASUS UVC HD
7. Audio:          Conexant Audio CX8050
8. Touchpad:       ELAN1200
9. Bios Version:   309

## Thing will not able to use

1. FN + media controller's key

## Known problems

1.  Not on X510UA-BQ490

## VoodooI2C

1. Polling mode for smooth movements and gestures
2. Finger ID-implemented VoodooInput and VoodooI2C for persistent gesture input

## Attention please
1. Note that this fork is mostly for my laptop only (X510UA-BQ490). If yours has similar features such as no dGPU and no KB backlight, try, but at your own risk. Otherwise, please go to tctien342's original master branch or hieplpvip's Zenbook repository.
2. If the versions of your VirtualSMC and corresponding efi driver and plugins do not match, touchpad and other battery issues may arise. Please make sure to download the most recent stable release of the SMC package and install them accordingly.

## Steps to install

1. Prepair a macOS installer in USB (Use creationmedia method or MBR HFS Firmware Check Patch available in https://www.insanelymac.com/forum/files/file/985-catalina-mbr-hfs-firmware-check-patch/ for both Mojave and Catalina).
2. Replace EFI folder in USB EFI partition with the EFI folder in Clover EFI.
3. Boot into USB and select macOS installer.
4. During the installation, touchpad does not work. You need a mouse connected through USB. (Or you may delete the IOGraphicsFamily dependency from VoodooI2CHID.kext/info.plist.) Follow installation instructions found on tonymacx86 or other hackintosh forums.
    - If you have chosen to install Catalina in HFS+ file system, follow the directions given in https://www.insanelymac.com/forum/files/file/985-catalina-mbr-hfs-firmware-check-patch/
5. After a successful installation, boot into macOS, copy kexts In /kexts/Other -> /Library/Extension.
6. Use Kext Utility (or simply copy this line without the quotation marks: "sudo chmod -R 755 /L*/E*&&sudo chown -R 0:0 /L*/E*&&sudo kextcache -i /") to rebuild kext cache then reboot.
    - If you have installed Catalina, use Hackintool to disable Gate Keeper beforehand. http://headsoft.com.au/download/mac/Hackintool.zip.
7. Now the touchpad and sound input should function correctly. You need to mount EFI and copy Clover EFI to the system EFI partition in like what you have done on USB EFI partition.
8. After System EFI replaced by your EFI, use Clover Configurator to change SMBIOS, generate your serial and MBL.
- Note: You may want to complete these extra steps.
    - You have DW1560 installed -- Replace WiFi/Bluetooth card
    - You have DW1560 installed but Bluetooth fails upon wake from sleep -- Set Bluetooth port as internal
    - You have not replaced the WiFi & BT module with DW1560 but want to have working iMessage and FaceTime with USB WiFi dongle or USB LAN -- Install RehabMan's Null Ethernet
    - You have Sleep and Airplane fn keys -- Activate Sleep and Airplane fn keys
    - You want to get rid of the control buttons to nonexistent keyboard backlight -- Remap PS2
    - You want to try a different _OSI patch -- Replace XOSI patch
    - (OpenCore) You want to enable full power management -- Unlock MSR 0xE2 (CFG Lock)


## Replace WiFi/Bluetooth card

1. Replace your stock card with DW1560.
2. Change the bootflag so you can specify your region. The default is brcmfx-country=#a.
3. Go to /kexts/other/additional and copy AirportBrcmFixup and the two Brcm kexts for Mojave (Repo and RAM2) and three for Catalina (Repo, Injector, RAM3) to /L*/E* and rebuild cache.
4. Optional: Copy /kexts/other/additional/LiluFriend.kext (recommended to create your own) to /L*/E* and rebuild cache.
5. Reboot.

## Set Bluetooth port as internal

1. Make sure USB injection kexts or SSDT-UIA.aml are not loaded.
2. Download Hackintool by headkaze http://headsoft.com.au/download/mac/Hackintool.zip
3. Under the USB tab, identify the Bluetooth port and set it as internal. The UVC camera can also be set as internal. Export and obtain the codeless injection kext and SSDTs in ~/Desktop. Delete the SSDTs.
4. Install the USBPorts.kext in /L*/E* (Refer to https://www.tonymacx86.com/threads/guide-usb-power-property-injection-for-sierra-and-later.222266/ for more information.).
5. Rebuild cache and reboot.

## Install RehabMan's Null Ethernet

1. Copy /kexts/other/additional/NullEthernet.kext to /L*/E* and rebuild cache.
2. Copy /ACPI/additional/SSDT-RMNE to /ACPI/patched.
3. Reboot.
- Note: For iMessage, FaceTime, and App Store to function correctly with RMNE, I recommend you install RMNE before trying to connect to any USB networking devices. Otherwise, you need to reset the network mapping by having RMNE set to en0. Use Google.

## Activate Sleep and Airplane fn keys

1. Follow the simple directions given in https://github.com/hieplpvip/AsusSMC/wiki/Installation-Instruction
    - Download Release of AsusSMC: https://github.com/hieplpvip/AsusSMC/releases
    - Run install_daemon.sh by dragging it onto terminal.
    - Reboot if the script does not seem to work. After updating your EFI folder or any kexts, you may need to run the script again.

## Remap PS2

1. Use MaciASL to save ACPI/additional/SSDT-PS2.dsl with the .aml extension in Patched folder.
2. Reboot.
- Optional: If you have a non-macOS USB keyboard, uncommenting the code in SSDT-PS2.dsl will swap left-cmd and left-alt. Install and configure Karabiner-Elements to switch back left-cmd and left-alt. This result in the same mapping in your PS2 keyboard and USB keyboard.

## Replace XOSI patch
Pick one of the below two patches.
### Insert _OSI for "Darwin"
1. Delete ACPI/Patched/SSDT-XOSI.aml.
2. Use MaciASL to save ACPI/replacement/SSDT-_OSI-XINI.dsl with the .aml extension in Patched folder.
3. Delete the _OSI and OSID patch in config.plist/ACPI/DSDT/Patches and copy OSYS patch from /replacement/config-_OSI-XINI.plist/ACPI/DSDT/Patches to config.plist.
4. Reboot.
### Assign OSYS "Windows 2015" value
1. Delete ACPI/Patched/SSDT-XOSI.aml.
2. Use MaciASL to save ACPI/replacement/SSDT-OSYS.dsl with the .aml extension in Patched folder.
3. Reboot.
- In my opinion, SSDT-OSYS.dsl is safer than SSDT-_OSI-XINI.dsl.

## Unlock MSR 0xE2 (CFG Lock)
- Note: You need to know the BIOS version matches your laptop model. Otherwise, there may be a permanent damage to your laptop.
1. Follow https://khronokernel-2.gitbook.io/opencore-vanilla-desktop-guide/extras/msr-lock.
2. The offset will be at 0x527 if your BIOS version is 309.

## When you think you are done

1. Update Clover, kexts, and efi files.
2. Backup your /L*/E* by copying them to the system EFI partition and/or installation USB EFI partition.

## Other things

1. OpenCore
    - Load CC from /L*/E*
    - Boot Windows
    - Need to configure BlessOverride
2. Clover
    - If you can't get Fn keys to work (namely touchpad enable/disable), try loading all kexts except CC from Clover in which case BrcmFirmwareData needs to load instead of BrcmFirmwareRepo.
## Changelog

March 23, 2020
- Use Finger ID-implemented VoodooInput and VoodooI2C for persistent touchpad gestures.

March 4, 2020
- Updated OpenCore to 0.5.6.

February 3, 2020
- Updated OpenCore to 0.5.4.

January 27, 2020 
- Updated kexts and Clover.
- AppleALC.kext stays on 1.4.2. Built AsusSMC.kext 1.2.0 with 10.15.2 and latest Lilu and VirtualSMC SDKs.
- Added NoTouchID.kext and other SMC sensor kexts.
- Deleted .efi files specific to FileVault. If needed, they can be obtained from the Release.

December 28, 2019
- Reverted back to AppleALC 1.4.2 with the layout-id 03000000, reinstalled CodecCommander and the corresponding SSDT: SSDT-CC.aml
- Reinstall USBPorts.kext to comply with ACPI specifications.

December 26, 2019
- Deleted USB configuration as not needed for working bluetooth on wake from sleep in 10.15.2: deleted USBPorts.kext.
- Changed SMBIOS to MacBookPro15,4 and added NoTouchID.kext.
- Switched CPUFriendDataProvider.kext to a new one: BPOWER-CPUFriendDataProvider.kext.

December 16, 2019
- Set the default touchpad patch to be SSDT-I2C1_USTP.aml.

-December 15, 2019
- Updated AppleALC to 1.4.4 and deleted CodecCommander and the corresponding patches.

October 28, 2019
- Added a simplified and minimum patch of the touchpad code in ACPI: SSDT-I2Cx_USTP.dsl.

October 26, 2019
- Update bluetooth kexts: BrcmPatchRAM2, BrcmPatchRAM3, BrcmFirmwareData, BrcmFirmwareRepo, BrcmBluetoothInjector.kext.

October 6, 2019
- Added the general touchpad polling mode patch that corresponds to the laptops in hieplpvip's repo: config-touchpad_general.plist, SSDT-ELAN-General.aml

October 4, 2019
- Separated fake EC device from main SSDT: SSDT-EC.aml.

September 28, 2019
- Deleted some entries in /config.plist/ACPI/DSDT/Fixes and added an SSDT to make the laptop behave more like MBP14,1: FixMutex, FixIPIC, FixHPET, SSDT-HPET.aml.

September 27, 2019
- Added back MATH and LDR2 devices: SSDT-MATHLDR2_STA.aml.

September 26, 2019
- Added another XOSI replacement patch: SSDT-OSYS.dsl.
- Added the steps to write your own touchpad SSDT.

September 13, 2019
- Option to replace XOSI patch by invoking If _OSI Darwin with SSDT-_OSI-XINI.dsl.
- Delete SSDT-PS2.aml and add SSDT-PS2.dsl with comments.

Prior to September 13, 2019
- Touchpad now uses polling mode with SSDT-X510UA-Touchpad.aml with assignment of FMCN and SSCN with activation from USTP=1 for proper clicks.
- Trimmed down SSDT-S510UA-KabyLakeR.aml and removed SSDT-RP01_PEGP for X510UA-BQ490.
- Removed XHC AAPL properties as not required as of Sierra.
- Removed Sinetek-rtsx.kext as the SD Card Reader is connected via USB.
- Removed SSDT-UIA.aml and included USBPorts.kext and information for proper USB configuration and working BT after sleep.
- Removed BT4LEContinuityFixup.kext and FakePCIID.kext and the FakePCIID plugin as AirportBrcmFixup, BrcmBluetoothInjector, BrcmFirmwareRepo, and BrcmPatchRAM2 are enough.
- Separated the RMNE device.
- Moved IGPU and HDEF contents from ACPI to config.plist.
- Made F5 and F6 keys do nothing with SSDT-PS2.aml as they are associated with keyboard backlight which X510UA-BQ490 does not have.

## Credits

Apple for macOS

tctien342 and hieplpvip for Asus repositories

the VoodooI2C helpdesk for working touchpad

headkaze for Hackintool

RehabMan for Null Ethernet and many other things

CrazyBird for HFS+ partitioning in 10.14+

daliansky and williambj1 for many hotpatch methods

LeeBinder for many helps

fewtarius for new CPUFriendDataProvider.kext and SMBIOS

## For Koreans
[README-kr](README-kr.md)를 참고하십시오.
