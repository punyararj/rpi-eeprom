# Raspberry Pi4 bootloader EEPROM release notes

USB MSD boot also requires the firmware from Raspberry Pi OS 2020-08-20 or newer.
https://www.raspberrypi.org/documentation/hardware/raspberrypi/bcm2711_bootloader_config.md

## 2021-06-17 - Avoid unnecessary PCIe probe on CM4 - BETA
   * Avoid default PCIe / XHCI probe on CM4 unless required for the current boot
     mode (USB_MSD or NVME).
   * Leave PCIe RC in reset state when loading start.elf except for USB-MSD mode.

## 2021-06-11 - Add USB_MSD_STARTUP_DELAY option - BETA
   * Minor update to BRCM SDRAM settings.
   * Add USB_MSD_STARTUP_DELAY option (default 0 option). This adds a configurable
     delay (in milliseconds) the first time the USB host controller is initialised
     before device enumeration.
     Normally, this should not be required. However, some HDD enclosures may
     require an extended startup delay in order to spinup drives. Without this
     the get-capacity command may stall and timeout.

## 2021-05-19 - Use the latest BRCM SDRAM settings - BETA
   * Use the latest BRCM SDRAM settings.
   * FAT12 support for small bootloader ramdisk images.
   * Minor file-system performance optimisations.
   * Added recovery.bin config.txt option (erase_eeprom=1) to perform an
     SPI chip-erase operation instead of programming the bootloader image.

## 2021-04-30 - Update default version to 2021-04-29
   * The manufacturing release has been updated to pieeprom-2021-04-29 so update the default release to match this.

## 2021-04-29 - Pi400 - Reduce MII clock freq when probing ethernet PHY - STABLE
   * Pi400 - Reduce MII clock freq when probing ethernet PHY - STABLE

## 2021-04-19 - Promote 2021-03-18 from LATEST to DEFAULT - DEFAULT
   * Display VC_BUILD_ID strings instead of the SHA256 hash
   * Add support for [cm4] and [pi400] config conditionals filters.
   * Change network boot to use the same "RXID" Ethernet PHY configuration as the 5.10 kernel
   * TFTP - reply to duplicate ACKS
   * Skip rendering of HDMI diagnostics display for the first 8 seconds unless an error occurs.
   * UDP checksum fixes
   * Add support for the BCM2711 XHCI controller - BOOT_ORDER 0x5
   * XHCI protocol layer fixes for non-VLI controllers
   * Avoid USB MSD timeout of there is only one device
   * Implement tryboot for OS upgrade fallback
   * Check the update-timestamp before applying an update in SELF-UPDATE mode

## 2021-04-13 - Fix error pattern for HDMI and SDRAM failures - BETA/STABLE
   * Fix recovery.bin error handler so that the LED error pattern is still
     displayed even if HDMI or SDRAM fail.

## 2021-03-18 - Fix occasional reboot fail on Pi4B pre 1.4 - STABLE
   * Fix GPIO expander reset issue on some Pi4B 1.1 to 1.3 boards

## 2021-03-17 - Fix issue with PCIe bridges in Linux - BETA
   * NVMe BETA boot support broke PCIe bridges in Linux. This should fix the problem

## 2021-03-04 - NVMe boot support - BETA
   * Adds support for NVMe to the bootloader with a new NVMe boot mode "6"
     NVMe currently only works for controller 0 on namespace 1 with a page size of 4096 bytes
     and block size of 512 bytes
   * The default boot order has been updated to F641 for cm4 ONLY, so NVMe boot is
     attempted after SD and USB

     To use the new NVMe add "6" to the BOOT_ORDER.

     This requires the latest rpi-update firmware to work or else you will see a compatibility
     error on boot. You also need the latest kernel from rpi-update to load rootfs from NVMe
     see https://github.com/Hexxeh/rpi-firmware/commit/48570ba954a318feee348d4e642ebd2b58d9dd97
     and https://github.com/Hexxeh/rpi-firmware/commit/e150906874ff8b9fb6271971fa4238997369f790

## 2021-02-22 - Promote 2021-02-16 to stable - STABLE (LATEST)
   * Freezing for default/critical update.

## 2021-02-16 - Change VC version info & TFTP fix - BETA
   * Display the VC_BUILD strings instead of the sha256 of the .elf file so that
     the information is the same as "vcgencmd version"
   * Change TFTP to ACK data blocks which it has already ACK'd instead of ignoring them.
   * Change network boot to use the same "RXID" configuration as the 5.10 kernel.

## 2021-01-16 - Fix 1V8 SD voltage reset for Pi 4B R1.1 - LATEST + BETA
   * Fix regression for GPIO expander reset change which caused PMIC reset
     to get card out of 1V8 mode to be missed.

## 2021-01-14 - Promote pieeprom-2021-01-11 to STABLE (LATEST)

## 2021-01-11 - Timeout stalled USB MSD devices - BETA
   * Timeout USB MSD commands and move to the next boot mode if a device stops responding.
   * Reset the GPIO expander at power on.
   * Use the bootloader build timestamp instead of zero for the update-timestamp
     if it is not defined in the .sig file.

## 2021-01-05 - USB MSD interop improvements for Pi 4B < R1.4 - BETA
   * Revert the USB port power delay on R1.1 boards to be more like the Sep 2020
     production release. Verified with Geekworm X835, Orico NVME M.2 USB adapter
     and Microsoft Wireless keyboard.
   * Increase the HDMI delay to 8 seconds.

## 2020-12-14 - Promote pieeprom-2020-12-11.bin to stable - STABLE
   * Feature freeze to support stable release of BCM2711 XHCI boot, tryboot,
     HDMI_DELAY, USB MSD improvements.

## 2020-12-11 - CM4/PI400 conditional filters - BETA
   * Add support for [cm4] and [pi400] config conditionals filters.
   * Tidyup RPIBOOT USB descriptors.
   * Add a gap before displaying LED error pattern and change the default state
     to off after displaying the first error pattern.
   * Generate 0xffff instead of 0x0 if the checksum of the UDP packet to be
     transmitted is 0x0.
   * Rename USB-DEV to RPIBOOT in boot-mode strings. Bootmode was renamed to
     avoid confusion with USB MSD boot.

## 2020-11-24 BCM2711 xHC boot support - BETA
   * Add support for booting from the BCM2711 XHCI controller which is the
     USB-C socket on Pi 4B / Pi 400 and the type A sockets on Compute Module 4
     IO board. The controller only supports USB 2.0 and the primary usage is
     for USB-MSD support on CM 4 boards without requiring a PCIe XHCI controller.

     To use this add '5' to the BOOT_ORDER in the EEPROM config for BCM_USB_MSD boot.

     This requires the latest rpi-update firmware.
     
     If start.elf is loaded via the BCM2711 XHCI (BOOT_ORDER 5) then the config.txt
     otg_mode setting will be set to 1 so that the OS can continue booting
     using the BCM2711 XHCI. This means that the device/gadget mode is not available
     when booted in this mode and there is no support for switching back to 
     the DWC2 controller from the BCM2711 XHCI controller.

   * Update halt behavior on Pi 400 to re-enable 'power on' button if the OS
     did a reset rather than using the standard mailbox shutdown commands. This
     overrides WAKE_ON_GPIO / POWER_OFF_ON_HALT settings on Pi 400 because
     it has a dedicated power button.
     If a button on GPIO3 really is requried then it can be re-enabled by setting
     WAKE_ON_GPIO=2 but that will consume more power.
   * Fix short blink before one-shot error pattern - #251
   * Validate SDRAM in recovery mode.
   * XHCI protocol layer fixes for non-VLI controllers.
   * Updated 'tryboot' for new version which also supports Pi3 and earlier.

## 2020-10-28 Defer HDMI diagnostics display, update-timestamps, tryboot support - BETA
   * Skip rendering of the diagnostics screen for HDMI_DELAY seconds (default 5).
     This means that for SD-card and USB MSD flash boot devices the diagnostics
     screen will not be visible.
   * On Pi 4B 1.4 (8GB) initialise SDRAM whilst waiting for the USB port power
     off time. This makes booting slightly faster.
   * Remove HDMI console messages where the information is duplicated elsewhere
     on the display.
   * Improve compatibility with external USB 3.0 disk enclosures by enumerating
     the downstream hubs before executing the USB port power off.
     N.B. Pi4 8GB automatically powers off the USB ports during chip-reset and
     does not need this change.
   * Don't timeout a USB MSD device after USB_MSD_LUN_TIMEOUT if there are no other
     MSD devices or LUNs to try. This avoids unnecessary timeouts on very slow
     to initialise disk drives e.g. USB HDDs designed for backups.
   * Fix failover to partition zero if the partition number is invalid. For USB
     MSD boot a start.elf update is also required.
   * SD-Card - Change default retries from 0 to 1 to improve reliability with
     some old SD v1 cards.
   * Fix issue where boot would stop if partition type 0x83 was encountered
     before the first FAT partition.
   * SELF_UPDATE mode (Network, USB MSD boot) now reads the timestamp information
     in pieeprom.sig created by rpi-eeprom-update to see if the updated is
     newer than the current 'update-timestamp'. If not, the update is skipped
     to avoid stale updates on network or USB disks being installed by accident.

     recovery.bin updates (from the SD card) do not check the timestamp because
     recovery.bin renames itself once the update is completed. However, it still
     writes the update-timestamp to the EEPROM.

     The update-timestamp is the timestamp when the update is created is
     independent of the build-timestamp for the bootloader executable. See
     rpi-eeprom-update -h
   * Add support for the 'tryboot' feature that enables operating systems to
     implement a fallback mechanism if an OS upgrade fails. This works with all
     bootable media types but requires updated firmware and OS software.

     This feature should be viewed as EXPERIMENTAL and may change depending upon
     feedback from other OS/distro maintainers.
     https://github.com/raspberrypi/linux/commit/757666748ebf69dc161a262faa3717a14d68e5aa


## 2020-10-02 Include CM4 manufacturing bootloader image.
   * Include the release image for reference. This contains some minor changes
     to support manufacture test.

## 2020-09-14 Promote the 2020-09-03 release to be the default EEPROM images.
   * Promote the 2020-09-03 bootloader EEPROM and VLI 0138A1 as the default
     release (critical folder).
    
   Interesting changes since 2020-04-16:-

   * Add support for booting from USB mass storage devices.
   * Add support for bootloader updates for USB MSD or network boot (self-update)
   * Improve compatibility for USB devices which require the USB port power
     to be switched off for a period of time during a reboot.
   * Add support GPT and Hybrid MBR partition tables.
   * Add support for EEPROM write-protect.
   * Add the ability to loop between different boot-modes until a bootable
     image is found. The default is to loop between SD and USB-MSD.
   * VLI 0138A1 - Improve full-speed isochronous endpoint support.


## 2020-09-07 Promote 2020-09-03 to release to STABLE

## 2020-09-03 Only use green LED for error status in bootloader - BETA
   * Turn the green LED on and leave it on unless an error code occurs.
     Previously, SD activity was displayed but that plus muxing with the
     SPI CS made the LED activity confusing.
     The HDMI diagnostics screen now provides much better information
     for determining if the bootloader is running or frozen.
   * CM4 enable GPIO for SD power.
   * Filename should be 2020-09-03

## 2020-08-31 Disable self-update from SD card - BETA
   * Since the ROM will load recovery.bin from the SD card self-update is not
     required. Although it functions correctly there is a small risk stale
     pieeprom.upd files would be installed automatically e.g. if the
     rpi-eeprom-update service has been disabled.

## 2020-08-10 Promote 2020-07-31 release to STABLE
   * The USB port power management change from the last BETA improves
     compatibility for devices which during reset with no regressions reported.
     Make this the latest stable release.

## 2020-07-31 Standardize USB port power control across board revisions - BETA
   * Turn off USB port power for 1-second regardless of boot-mode. This appears
     to resolve an issue on R1.3 and older board revisions where some USB
     devices would fail upon reboot. On R1.4 USB port power is turned off
     automatically by the PMIC so this is just held in reset for longer. For
     earlier board revisions the USB port power is explicitly turned off via
     XHCI.
     This can be overridden via USB_MSD_PWR_OFF_TIME in the EEPROM config.
   * Update to the latest Broadcom memsys FW - no significant functional change.

## 2020-07-20 Promote 2020-07-16 bootloader and VL805 0138A1 FW to stable - STABLE
   * Promote the latest beta to stable as the next production firmware release
     candidate.
     The main difference between this and the previous stable version is
     the VL805 FW update. 

## 2020-07-16 Update VL805 FW to 0138A1 and add optional EEPROM write-protect - BETA
   * Patch previous 2020-07-16 from c44ee87f -> 45291ce6 to fix a CM4 specific
     issue which does not impact Model B
   * Update the VL805 embedded / standalone FW version to 0138A1
      *  User settings of the ASPM bits in the PCI configuration space
         link control register are now maintained
      * Better full-speed Isochronous endpoint support
   * Add eeprom_write_protect config.txt variable which if set configures
     the non-volatile status register bits to define the write protect
     regions.
      * If 1 then configure the write protect regions for both the
        bootloader and VLI EEPROMs to cover the entire EEPROM.
      * If zero then clear all write protect bits.
      * If missing or -1 then don't change the current state.
   * The write protect is only effective if the /WP pin is pulled low
     e.g. by shorting TP5 to ground.
   * WARNING: Previous versions of the bootloader, recovery.bin and vl805
     tool do NOT clear the non-volatile status bits for the VL805 SPI EEPROM.
     Consequently, installing an older version will fail/hang if the write
     protect bits have not been cleared first (eeprom_write_protect=0)
   * Update the vl805 user-space tool to clear the WP bits.
   * Add recovery_wait config.txt option which if set to 1 forces the EEPROM
     rescue image and flashes the activity LED forever. This is intended for
     use with an SD card image which just contains recovery.bin + config.txt
     and is used to set/clear WP on multiple boards.
   * The write protect functionality works with self-update mode, however,
     the bootloader must have already been updated to the version supporting
     write protect first i.e. at least two reboots are required.
   * Update the HDMI diagnostics screen to display 'RO' after the EEPROM version
     if the write status register for the bootloader SPI EEPROM has write protect
     bits defined. This does NOT attempt to verify if /WP is low.

## 2020-07-06 Tweak USB port power and clear ACT LED after SPI - BETA
   * Increase port power off limit to 5 seconds.
   * Increase the port power off default to 1 second. This seems to cover most
     commonly seen USB MSD devices which require the USB port power to be disabled
     after the USB HC chip is reset.
   * Reset activity LED after SPI access to reduce the number of spurious LED flashes.
   * Add SPI error diagnostic error code (3 long 1 short) if SPI commands timeout.
     (So far this failure has not been observed on failed boards)

## 2020-06-17 Promote 2020-06-15 to STABLE
   * Promote the latest beta EEPROM and recovery.bin to stable and
     feature freeze USB MSD support until a production release is ready.

## 2020-06-15 Increase default USB port power delay - BETA
   * Increase the default power off delay to 500ms following more
     interop testing.
   * Make the USB port power off time configurable via the USB_MSD_PWR_OFF_TIME
     config. The range may be set between 250 and 1000ms. Zero means no port
     power off.
   * Fix some issues in XHCI endpoint configuration where the code was wrong
     but does not fail with the current VL805 FW.

## 2020-06-12 Improve support for powered USB SATA devices - BETA
   * Reset Ethernet MAC + PHY if final boot mode is not network boot
     See: Kernel warning and network failure when attempting to use the network after bootloader times out. #144
   * Improve handling of multiple bootable USB devices and remove USB_MSD_BOOT_MAX_RETRIES
   * Resolve: No DHCPACK with DHCP relay agent #58
   * Toggle USB root hub port power for 200ms on the first USB MSD boot attempt
     See: Bootloader can't boot via USB-HDD after system reboot #151
   * Update bootloader handover to support uart_2ndstage - requires
     a newer start.elf firmware which will be via rpi-update.
   * Assert PCIe fundamental reset if the final bootmode was not USB-MSD because
     the OS might not do this before starting XHCI.

## 2020-06-03 Bootmode tweaks and fix issue with > 4TB drives - BETA
   * Resolve: Unable to boot from USB MSD - Seagate 5Tb HDD backup drive #139
   * Increase USB MSD timeout from 10 to 20 seconds.
   * Max retries now default to zero because the default BOOT_ORDER includes
     restart (0xf). Therefore, each boot-mode is now tried once before moving
     to the next mode. The retries mechanism is largely redundant now that
     the loop/restart mode exists.
   * If TFTP fails and network boot retries > 0 then wait 5 seconds before
     retrying to avoid overloading a misconfigured TFTP server.
   * Map undefined boot-modes in BOOT_ORDER to SD (0x1) instead of stopping.
   * Add missing pieeprom-2020-05-28

## 2020-05-28 Secondary fix for VL805 readback issue - BETA
    * Re-upload 2020-05-28 after Git issue
    * rpi-eeprom-update for new board revisions

## 2020-05-27 Fix DPI issue - BETA
    * Resolve: DPI failure after HDMI diagnostics screen in beta bootloader #133
    * Resolve: VL805 readback failure in the bootloader #134

## 2020-05-26 USB MSD updates - BETA
    * Resolve: USB boot fails if the GPT contains no basic data or EFI partitions #130
    * Resolve: Fix default BOOT_ORDER in mass storage beta #129
    * Resolve: Add support for booting from a "superfloppy" disk #120
    * Resolve: USB MSD timeout message - incorrect units #131
    * Resolve: Recognize efi partition (0xef) as a valid boot #126
    * The HDMI diagnostics screen now displays the most significant bytes
      of the SHA-256 of the config.txt file.

## 2020-05-26 rpi-eeprom-update
    * Mark USE_FLASHROM as deprecated.
    * Resolve: Unnecessary check for '*.elf' in BOOTFS #92
    * Update help for FIRMWARE_RELEASE_STATUS.

## 2020-05-15 Add pieeprom-2020-05-15 beta with USB boot
    * USB mass storage boot will NOT work without the updated firmware
      start.elf binaries. These will probably be released via rpi-update
      in a few days’ time.
      This release simply helps to validate if there are regressions in
      the current SD and Network boot modes.

      * SELF_UPDATE and bootloader_update are now enabled by default.

## 2020-05-11 Garbage collect old binaries
    * Now that 2020-04-16 is has been released as the default production
      release move the old binaries to an old (deprecated) directory.
      These can be removed for the APT package to reduce disk space.

## Promote 2020-04-16 EEPROM release critical
    * Make this the default release for all users. This supports network
      boot, configurable boot order and HDMI diagnostics screen.

## 2020-04-16 Promote to stable
    * The PLL analog changes in the beta release never made it to stable.
      Skip straight 2020-04-16 to synchronize releases.

## 2020-04-16 Revert PLL analog changes
    * This seems to cause problems on some firmware releases if enable_tvout
      is set due to different behaviour in PLL management.

## 2020-04-12 Update beta+stable recovery.bin
    * If the VL805 image was updated but the bootloader was not then
      recovery.bin would incorrectly switch to infinite flashing activity
      LED pattern used in the rescue image to prevent infinite reboots.
      Fix recovery.bin to reboot in this case. The current 'critical'
      release does not have this problem.
    * Fix uart_2ndstage logging in beta/stable recovery image.
    * Change recovery.bin to reboot instead of displaying an error pattern
      if there are no EEPROM images. The Raspberry Pi Image makes it very
      difficult to create a broken rescue image but a stray recovery.bin
      could stop Raspbian from booting.
    * Fix detection of VL805 EEPROM in recovery.bin

    N.B. These recovery.bin file used for critical updates and the rescue
    image does not suffer from these bugs.

## 2020-04-09 Add 2020-04-09 beta firmware.
    * Experimental tweaks for PLL analog setup to reduced jitter and
      improve PCIe reliability on slower silicon.

## 2020-04-07 Promote 2020-03-19 beta firmware to stable.
    * No major bugs reported. Promote this to stable as a step
      towards getting HDMI diagnostics into the default firmware
      via a critical update.

## 2020-03-19 Add 2020-03-19 beta firmware
    * Minor mods for manufacture test.

## 2020-03-16 Add 2020-03-16 beta firmware
    * Fix DHCP Option97 GUID generation. The MAC LSB portion was previously
      always zero.

## 2020-03-11 Add 2020-03-04 beta firmware recovery
    * Support static IP address configuration. The following fields may be
      set manually using dotted decimal address. If set, then DHCP if skipped.
       * CLIENT_IP
       * SUBNET
       * GATEWAY
       * TFTP_IP
    * If a fatal bootloader error occurs then an HDMI diagnostics screen is
      displayed at VGA/DVI resolution on both outputs for two minutes.
      This may be disabled by setting DISABLE_HDMI=1 in the EEPROM
      configuration OR setting display_splash=1 in config.txt.
    * Allow the PXE menu option to match a custom string specified by
      PXE_OPTION43. The default is still "Raspberry Pi Boot"
    * DHCP_OPTION97 - The default GUID has now changed to
      RPI4+BOARD_ID+ETH_MAC_LSB+SERIAL in order to make it easier to
      automatically identify Raspberry Pi computers. The old behaviour
      is enabled by setting DHCP_OPTION97=0 which simply repeats the serial
      number 4 times.
    * SELF_UPDATE. If SELF_UPDATE is set to 1 in the EEPROM configuration AND
      config.txt contains bootloader_update=1 then the bootloader will be looking
      for pieeprom.upd and vl805.bin and apply these firmware files if
      they are different to the current image, before doing a watchdog reset.
      This should make it easier to update the bootloader for network
      booted setups because an SD card is not required for recovery.bin.
      As usual, TFTP should only be used on private networks because the
      protocol is not secure against spoofing.
    * recovery.bin. The beta recovery.bin will now display a green screen
      via HDMI if successful or red if a failure occurs.

## 2020-02-27 rpi-eeprom-update & firmware
    * Remove the dependency check for the vl805 utility. This is deprecated
      and there is no 64-bit version. The file is still available in Github
      for anyone who wants to continue using USE_FLASHROM or create their
      own scripts.
    * Add a stable firmware directory based on the latest beta release.
      Stable should be interpreted as feature-freeze releases. In this
      case the core network boot is stable enough for most scenarios
      and this de-risks adding new more experimental features in the
      beta folder.

## 2020-01-22 - vl805 00137ad
    * Set the default/critical vl805 version to be 00137ad. This has the
      same power savings as 0137ab but with fixes for USB webcams.

## 2020-01-17 - Git 5e86aac5f (BETA) RC4
    * Handle DHCP option 0 - padding
    * Fix SD card voltage detection

## 2020-01-14 - rpi-eeprom-config
    * Fix padding calculation

## 2020-01-09 - Git df0ff18c (BETA) RC3
    * Fix parsing of multiple menu entries in PXE options.
    * Fix regression in IP address parsing

## 2019-12-03 - Git f0d7269d4 (BETA) RC2
    * Fix handling of multiple menu options with TFTP Option43
    * Ignore unsupported modes in BOOT_ORDER instead of stopping.

## 2019-11-18 - Git b6a7593d6 (BETA) RC1
    First release candidate before this beta is moved to a stable release series.

    * Avoid resetting TFTP prefix after retries or if start4.elf is not found.
    * Add MAC_ADDRESS option which allows the OTP Ethernet MAC address to be
      overridden. A VideoCore firmware update will propagate this forced
      mac address to device-tree/cmdline in the near future.
    * Various internal refactorings to prepare for USB MSD storage boot in
      the next beta-series.
    * Enable high-speed mode for EMMC cards.

## 2019-10-17 - rpi-eeprom-update + recovery.bin
    * New beta recovery.bin which can update the VLI EEPROM before
      start.elf is loaded. This is the recommended and default method
      because no USB devices will be in use at this stage.
    * Extend the USE_FLASHROM configuration to use the vl805 tool
      to program the VL805 directly.
    * Generate SHA256 checksums in .sig files for the bootloader and
      and VL805 images. This is required by the new recovery.bin to
      guard against corrupted files being flashed to the EEPROM(s).
    * Various variable renames to distinguish between the bootloader
      and the VL805 images.

## 2019-10-16 - Git 18472066 (BETA)
   * Ignore trailing characters when parsing in PXE boot menu option.
   * Improve error handling with unformatted sd-cards.
## 2019-10-08 - Git 26dd3686c (BETA)
   * TFTP now uses RFC2348 blksize option to get 1024 byte blocks if the server supports it.
   * Fix DHCP handling of SI_ADDR
   * TFTP_PREFIX and TFTP_PREFIX_STR options for mac-address or string literal prefix.
   * Improved support for standard capacity and SDv1 cards.

## 2019-09-25 - Git 4d9824321 (BETA)
   * Increase TFTP timeout to 30s as default & bootconf.txt
   * Fix intermittent boot freeze/slowdown issue after loading start.elf
   * Don't load start.elf during network boot if start4.elf exists but the download times out.
## 2019-09-23 - Git c67e8bb3 (BETA)
   * Add support for network boot
   * Configurable ordering for boot modes (BOOT_ORDER and SD/NET_BOOT retries)

## 2019-09-10 - Git f626c772
   * Configure ethernet RGMII pins at power on. This is a minor change which
     which may improve reliability of ethernet for some users.

## 2019-09-05 - Git d8189ed4 - (BETA)
   * Update SDRAM setup to reduce power consumption.

## 2019-07-15 - Git 514670a2
   * Turn green LED activity off on halt.
   * Pad embedded config file with spaces for easier editing by end users.
   * Halt now behaves the same as earlier Pi models to improve power behavior at halt for HATs.
   * WAKE_ON_GPIO now defaults to 1 in the EEPROM config file.
   * POWER_OFF_ON_HALT setting added defaulting to zero. Set this to 1 to restore the behavior where 'sudo halt' powers off all PMIC output.
   * If WAKE_ON_GPIO=1 then POWER_OFF_ON_HALT is ignored.
   * Load start4db.elf / fixup4db.dat in preference to start_db.elf / fixup_db.dat on Pi4.
   * Embed BUILD_TIMESTAMP in the EEPROM image to assist version checking.

## 2019-05-10 - Git d2402c53 (RC2.1)
   * First production version.
