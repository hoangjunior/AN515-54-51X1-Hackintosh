# Guide edit BIOS value using RUEXE

# NOTE

**You are modifying your BIOS here. This isn't without risk and you can easily break your laptop with it. Proceed with caution and DON'T just try "something". Only modify if you're sure. I'm not responsible for your mistakes and broken devices.**

### 1: Dump BIOS

If you do not have Intel ME drivers installed, install them now from your system driver download page, then start over here after reboot. 
Check your BIOS' main page and see if ME FW version is shown. If not then download HWINFO64

Once HWINFO is open, look at the large window on the left side, expand motherboard, and find the ME area. 
Inside that section is the ME Firmware version. Take note of the version. (ie. write it down or get a screenshot)

Once you have that, go to the thread linked below, and in the section "C.2" find and download the matching ME System Tools Package for your system.
(ie if ME FW version = 10.x get V10 package, if 9.0-9.1 get V9.1 package, if 9.5 or above get V9.5 package etc)
[DOWNLOAD " ME System Tools " packages HERE](https://www.win-raid.com/t596f39-Intel-Management-Engine-Drivers-Firmware-amp-System-Tools.html)

Once downloaded, inside you will find Flash Programming Tool folder, and then inside that a Windows or Win32/Win32 folder (NOT x64).
Highlight that Win/Win32 folder, then hold shift and press right click. Choose "open command window here".

Now you should be at the command prompt. 
You are going to BACKUP the factory un-modified firmware, so type the following command: 
Command: " FPTw.exe -bios -d biosreg.bin "

### 2: Finding BIOS variable

1) Open UEFITool > File > Open image file... > select "All files (*)" on the corner > choose biosreg.bin
2) Ctrl F and search **CFG Lock**, a message will be displayed, double click on the message, the entry/module should be selected displaying additional information. Action > File > Extract as is... > save as setup.bin
3) Download [Universal IFR Extractor](https://github.com/LongSoft/Universal-IFR-Extractor/releases)

Open your setup.bin with ifrextract

``
path/to/ifrextract path/to/Setup.bin path/to/Setup.txt
``

4) Open Setup.txt and search for CFG Lock

"Variable:" will show the offset. In my case the offset is 0x3E. The value stored in that address is the actual CFG Lock and we need to set to 0

### 3: Create a bootable USB with RU

To actually change the BIOS Lock, we can't do it directly from the operating system, we need an utility called RU:
RU homepage: [RU](http://ruexe.blogspot.com)
There should be 3 files inside: RU.efi, RU.exe and RU32.efi

- Download Rufus and open it as administrator.

- Select from the device list your pendrive or card, with the following options, and hit Start. It will delete all the data on that device:
Partition scheme and target system type: MBR partition scheme for UEFI
File system: use FAT32. FAT (Default) will fail to format and NTFS will fail to save screenshots from RU itself.
Quick format
Uncheck "Make a bootable disk using"
- Now, browse to the unit with Windows explorer (in my case it's H: drive) and create the folder EFI on the root of the pendrive and another folder BOOT inside EFI.

- Copy the downloaded file RU.efi to H:\EFI\BOOT and rename it to bootx64.efi

Now you have a bootable USB pendrive with RU.

### 4:Finding and disable CFG Lock

Restart and press F12, select your USB then this will show up

![](/BIOS/20200823080754.bmp)

Press **ALT =**, a list of UEFI variables will be displayed in alphabetical order. Use the keyboard arrows to move down until you see "CpuSetup"

![](/BIOS/20200823080810.bmp)

and then with the arrow keys move to row 0030, and then to the column 0E, until you are at position 003E

![](/BIOS/20200823080817.bmp)

Just type **0** and the value in that position will change to **00** in red, meaning that it's in edit mode. Type Enter to accept the new value

![](/BIOS/20200823080830.bmp)

Finally, CTRL+W will save changes permanently to the BIOS. You should see a "Updated OK: Setup" message

![](/BIOS/20200823080836.bmp)

To quit, press **ALT Q**
