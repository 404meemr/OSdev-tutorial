# CPU Startup
## Chapter 0 Section 1

### **Vocabulary**
| Acronym | Name | Definition |
| ------- | ---- | ---------- |
| RAM | Random Access Memory | yes |
| UEFI | Unified Extensible Firmware Interface | A replacement for legacy BIOS |
| NVRAM | Non-Volatile Random Access Memory | Memory that maintains its data even after power loss |
| BIOS | Basic Input/Output System | Firmware used to bootstrap and control hardware | 
| MMU | memory Management Unit | Chip that controls all memory access |
| POST | Power On Self Test | Systems check |

&emsp;When the CPU first starts up, it has no knowledge about anything. The first thing it does is make sure that the power supply is providing a reliable source of power. If not, the CPU will generate a reset signal until it recieves a good supply. A reset signal is similar to turning the CPU off and on again. Right now the CPU doesn't know what peripherals you have, how to talk to your HDDs or SSDs, and not even the existence of RAM. Your computer obviously needs some way to initialize and bootstrap everything, so the CPU jumps to a reset vector. On x86_64 systems, this vector is 0xFFFFFFF0.

&emsp;The BIOS ROM code is either stored in the UEFI NVRAM or on the BIOS chip. Since RAM is not initialized yet, any calls to memory will bypass the RAM chips and instead access either the UEFI NVRAM or BIOS chip. The code here then sets up the rest of the CPU and RAM. If this address is not available, the CPU will fault and die, as this is Undefined Behavior (#UD). Once this is done, the redirects to the NVRAM or ROM are disabled and all memory access will now go to the RAM chips. 

&emsp;The CPU, however, does not do this itself. There is a separate chip on the CPU called the MMU, or Memory Management Unit. This chip handles all memory access, and so the CPU simply calls upon the MMU to do its thing. Once this is done, the BIOS goes on to bootstrap the rest of the system.

&emsp;First, the BIOS performs a "Power On Self Test", or POST for short. If there are any fatal errors, the boot process is stopped and the firmware decides what to do. If not, the BIOS calls Interrupt 19, which is used to start the process of loading the operating system. Then, the BIOS first looks for a video card's BIOS and runs it. This is normally located at memory address 0xC000. This video card BIOS then initializes the rest of the card. After the video card, the BIOS starts looking for Floppy Drives, Hard Drives, and other persistent storage devices. Now, the BIOS searches for other hardware and interrogates them to make sure they are working correctly. At this point, the BIOS will also search for and find logical devices like serial (COM) and parallel (LPT) ports.

&emsp;Once this is done, the BIOS begins its search for a boot drive to load an operating system. Most BIOSes will first start looking for a bootable floppy drive and <i>then</i> a bootable hard drive. The method to determine this will be covered in the next section. When a bootable drive is found, the BIOS will read the contents of Cylinder 0, Head 0, Sector 1 of the disk, load it into memory, and start executing this. This is the code that will be your bootloader, or if you have a partition scheme, the MBR/GPT bootstrap code. The code that resides here will be used to load your kernel.

Next Up: <a href="0-2_bootloader.md">The Bootloader</a>