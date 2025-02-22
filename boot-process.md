# Linux Boot Process Explained (UEFI and systemd)

This document provides a detailed explanation of the Linux boot process, focusing on the modern UEFI-based approach with systemd.

## Stages of the Boot Process

1. **UEFI Firmware Initialization:**
   - UEFI (Unified Extensible Firmware Interface) is the modern firmware standard, replacing BIOS.
   - It performs Power-On Self-Test (POST).
   - UEFI reads boot entries from the **EFI System Partition (ESP)**.

2. **EFI System Partition (ESP):**
   - A dedicated partition (typically FAT32 formatted) on the boot device.
   - Contains boot loaders (e.g., GRUB2) for installed operating systems.
   - UEFI uses boot entries in the ESP to locate and start the boot loader.

3. **GRUB2 (UEFI-compatible Boot Loader):**
   - GRUB2 (GRand Unified Bootloader version 2) is a common boot loader that supports UEFI.
   - It loads the Linux kernel and initial RAM file system (initramfs) into memory.
   - GRUB2 can present a boot menu to select different kernels or operating systems.
   - It reads its configuration from files within the ESP.

4. **Kernel and Initramfs Loading:**
   - **Kernel:** The core of the Linux operating system. It manages system resources (CPU, memory, devices).
   - **initramfs:** A temporary root file system (often a compressed cpio archive). It contains essential modules and drivers needed *before* the real root file system can be mounted.

5. **Kernel Initialization:**
   - The kernel decompresses itself and initializes hardware.
   - It executes `/sbin/init` (or a symbolic link to it), which points to `systemd`.

6. **Systemd Initialization:**
   - **systemd:** The system and service manager (init system). It replaces the older System V init.
   - It's the first process started by the kernel (PID 1).
   - systemd executes `initrd.target` (or a similar target) from the initramfs to set up the basic system environment.

7. **Mounting the Root File System:**
   - The actual root file system (e.g., on `/dev/sdaX`) is mounted.
   - The root file system is switched from the initramfs to the actual root partition on the hard drive.  This transition is crucial.

8. **Systemd Target Selection:**
   - Systemd reads the symbolic link `/etc/systemd/system/default.target`.
   - This link points to a target file (e.g., `/usr/lib/systemd/system/multi-user.target` for a multi-user system, or `/usr/lib/systemd/system/graphical.target` for a graphical desktop).

9. **Systemd Service Startup:**
   - The target file defines a set of systemd "units" (services, daemons, etc.) that should be running in that target environment.
   - Systemd starts these units and their dependencies, bringing the system to the defined target state (similar to a runlevel in older systems).

## Key Concepts

*   **UEFI:** Modern firmware interface.
*   **ESP:** EFI System Partition (contains boot loaders).
*   **GRUB2:** Boot loader.
*   **Kernel:** Core of the OS.
*   **initramfs:** Temporary root file system.
*   **systemd:** System and service manager.
*   **Target:** Defines a system state (collection of services).

## Boot Process Analogy

Think of the boot process as a play:

*   **UEFI:** Stage manager (checks equipment).
*   **GRUB2:** Director (loads actors).
*   **Kernel:** Lead actor (takes charge).
*   **initramfs:** Assistant (gets kernel ready).
*   **systemd:** Producer (sets up the show).
*   **Targets:** Script (defines roles).
