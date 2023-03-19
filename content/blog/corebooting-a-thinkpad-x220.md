---
title: "Corebooting a Thinkpad X220"
date: 2022-05-31T20:50:37-05:00
draft: false
---

{{< img caption="My Thinkpad X220" src="/p/thinkpad.webp" >}}

## You Need

- A Thinkpad X220
- A Raspberry Pi
- Female to female jumper wires
- SOIC8 test clip
- Another computer

## Disassembly

For disassembly you can watch my video [here](https://www.youtube.com/watch?v=hERguULT7Vo).

But you’ll just have to remove all the screws with the keyboard icon and all the screws with the box(ish) icon.
(Like I said, you can watch the video).

## Attaching the clip to the BIOS chip

In order to actually read/write to the BIOS chip you need to attach the SOIC8 clip to the bios chip.

### X220 BIOS pinout

```
                   ______
        MOSI  5 --|      |-- 4  GND
         CLK  6 --| BIOS |-- 3  No Connection
No Connection 7 --|      |-- 2  MISO
   VCC (3.3V) 8 --|______|-- 1  CS
```

### Raspberry Pi pinout

```
                        CS
  1                     |           20
+-----------------------v-------------+
| x x x x x x x x x x x x x x x x x x |
| x x x x x x x x x x x x x x x x x x |
+-----------------^-^-^-^-------------+
 21               | | | |           40
                VCC | | CLK
               MOSI/   \MISO
```

## Setting up the Raspberry Pi

Make sure to update your Raspberry PI and install and the needed packages as well as flashrom using these commands:

``` sh
sudo apt-get update && sudo apt-get upgrade
sudo apt-get install build-essential pciutils usbutils libpci-dev libusb-dev libftdi1 libftdi-dev zlib1g-dev
git clone https://review.coreboot.org/flashrom.git
cd flashrom
make -j3 && sudo make install
```

Now we need to download the Coreboot repo on our Raspberry PI.

``` sh
git clone --recursive https://review.coreboot.org/coreboot.git ~/coreboot
```

Next we need to install ifdtool on the Raspberry PI, you can do that by running:

``` sh
cd ~/coreboot/util/ifdtool
make -j3 && sudo make install
```

## Reading the BIOS

First, we are going to create an alias so we don’t need to type in a long drawn out command every time we want to read/write to the BIOS.

``` sh
alias fr='sudo flashrom -p linux_spi:dev=/dev/spidev0.0,spispeed=1024'
```

Now we can get the name of our BIOS chip by just running `fr`.

``` sh
fr
```

The output should give you multiple chip names.
All of these are the same chip just with different names so you can use any of them, mine is “MX25L6405”.
We are going to use this to set a `CHIP` variable.

``` sh
CHIP="MX25L6405"
```

We are now ready to read the flash from the BIOS chip.
We are going to do this a few times in order to make sure that the connection is consistent when reading and writing.

``` sh
fr -c "$CHIP" -r flash01.bin
fr -c "$CHIP" -r flash02.bin
fr -c "$CHIP" -r flash03.bin
md5sum flash01.bin flash02.bin flash03.bin
```

The output for `md5sum` for all three of the files should be exactly the same.
If the checksum for all three files are not the same then **DO NOT CONTINUE!!!**
Make sure that your connection is good and retry until everything reads correctly.
(If necessary, the spispeed can be lowered from 1024 for a more reliable read).

## (Optional) Removing the management engine

First we need to download me_cleaner.

``` sh
git clone https://github.com/corna/me_cleaner ~/me_cleaner
```

Now we can run me_cleaner on our flash file, in this case I will be using `flash01.bin`.

``` sh
~/me_cleaner/me_cleaner.py -S flash01.bin
```

If all goes well you should see a message that says: `Done! Good Luck!`

## Separating the image

Now we can run ifdtool on our flash image in order to separate it.

``` sh
ifdtool -x flash01.bin
```

You should now have four different `.bin` files: 1. `flashregion_0_flashdescriptor.bin` 2. `flashregion_1_bios.bin` (Not needed) 3. `flashregion_2_intel_me.bin` 4. `flashregion_3_gbe.bin`

We can now rename all the files to have a shorter name.

``` sh
mv flashregion_0_descriptor.bin descriptor.bin
mv flashregion_2_intel_me.bin me.bin
mv flashregion_3_gbe.bin gbe.bin
```

## Setting up Coreboot

If you want to compile Coreboot on your Raspberry PI you can go ahead, however it might take anywhere from a few hours to a few **DAYS**, so be warned.
I copied my “.bin” files to my laptop in order to compile faster.

Now we want to download the Coreboot repo onto our computer that we are compiling Coreboot on.
(This may take a while).
(If you are compiling Coreboot on your Raspberry PI you can skip this).

``` sh
git clone --recursive https://review.coreboot.org/coreboot.git ~/coreboot
```

> ## (Optional) Downloading VGA BIOS
> Windows and some Linux distributions rely on the VGA BIOS in order to display video.
> So you can optionally download it if you need it.
> ``` sh
> curl -fLO "https://github.com/thetarkus/x220-coreboot-guide/raw/master/vga-8086-0126.bin"
> ```

Now we need to make a directory to place our “.bin” files.

``` sh
mkdir -p ~/coreboot/3rdparty/blobs/mainboard/lenovo/x220
mv descriptor.bin ~/coreboot/3rdparty/blobs/mainboard/lenovo/x220/
mv me.bin ~/coreboot/3rdparty/blobs/mainboard/lenovo/x220/
mv gbe.bin ~/coreboot/3rdparty/blobs/mainboard/lenovo/x220/
```

## Configuring Coreboot

On the computer you’re compiling Coreboot with, you’ll need to install these development packages (or their equivalents).
On Ubuntu, Debian, or any derivative you can run:

``` sh
sudo apt-get install git build-essential gnat flex bison libncurses5-dev wget zlib1g-dev
```

On Void Linux (what I use) I ran:

``` sh
sudo xbps-install git base-devel ncurses-devel wget zlib-devel gcc-ada
```

Now we can go into the Coreboot directory and run make `nconfig`.

``` sh
cd ~/coreboot
make nconfig
```

You should see a menu pop up, now we can configure our Coreboot build.
Below is a list of what needs to be enabled, you can leave the rest of the settings just the way they are.

```
General Setup
    - [*] Compress ramstage with LZMA
    - [*] Include coreboot .config file into the ROM image
    - [*] Allow use of binary-only repository

Mainboard
    - Mainboard vendor (Lenovo)
    - Mainboard model (Thinkpad X220)
    - ROM chip size (8192 KB (8 MB))
    - (0x100000) Size of CBFS filesystem in ROM

Chipset
    - [*] Enable VMX for virtualization
    - Include CPU microcode in CBFS (Generate from tree)
    - Flash ROM locking on S3 resume (Don't lock ROM sections on S3 resume)
    - [*] Add Intel descriptor.bin file
      (3rdparty/blobs/mainboard/$(MAINBOARDDIR)/descriptor.bin) Path and filename of the descriptor.bin file
    - [*] Add Intel ME/TXE firmware
      (3rdparty/blobs/mainboard/$(MAINBOARDDIR)/me.bin) Path to management engine firmware
    - [*] Add gigabit ethernet firmware
      (3rdparty/blobs/mainboard/$(MAINBOARDDIR)/gbe.bin) Path to gigabit ethernet firmware
      
Devices
    - Graphics initialization (Run VGA Option ROMs)
    - [*] Use native graphics initialization
    - [*] Add a VGA BIOS image
      (/home/$USER/vga-8086-0126.bin) VGA BIOS path and filename
      (8086,0126) VGA device PCI IDs
      
Generic Drivers
    - [*] PS/2 keyboard init
    - [*] Support Intel PCI-e WiFi adapters

Console
    - [*] Squelch AP CPUs from early console.
    - [*] Show POST codes on the debug console

System tables
    - [*] Generate SMBIOS tables

Payload
    - Add a payload (SeaBIOS)
    - SeaBIOS version (master)
    - (3000) PS/2 keyboard controller initialization timeout (milliseconds)
    - [*] Harware init during option ROM execution
    - [*] Include generated option rom that implements legacy VGA BIOS compatibility
    - [*] Use LZMA compression for payloads
```

You can press `F6` to save your config and then press `F9` to exit.
Now we can actually compile Coreboot now.

> ### (Optional) Create Cross Compiler
> If you don’t have an `i386` cross compiler you can make one by running:
> ``` sh
> make crossgcc-i386
> make iasl
> ```

Let’s compile coreboot by running:

``` sh
make -j$(nproc)
```

This might take a while.

**NOTE:** If you can’t compile Coreboot, try checking and making sure you did everything correctly.

## Flashing Coreboot

**WARNING: Proceed with caution, you can possibly brick your computer if you are not careful!!!**

You should now be left with a file named `coreboot.rom` in the `~/coreboot` directory.
You can copy this file back to your Raspberry PI into order to flash it.

Now let’s go ahead and read our flash chip again to make sure that our connection is still good.

``` sh
fr -c "$CHIP" -r flash01.bin
fr -c "$CHIP" -r flash02.bin
fr -c "$CHIP" -r flash03.bin
md5sum flash01.bin flash02.bin flash03.bin
```

And, like before, if all the checksums match, you can go ahead and flash `coreboot.rom`.

``` sh
fr -c "$CHIP" -w coreboot.rom
```

Now, for the moment of truth, go ahead and boot your Thinkpad.
If it won’t boot, don’t sweat, just rebuild and try again.
If Coreboot won’t work on your Thinkpad no matter what you try, then you can just flash a backup of the BIOS that you read earlier and your computer should work just fine.

## Aftermath

**Congrats!!!** You successfully freed your computer from the spying eyes of Intel and your local three letter government agency.
You can now enjoy your computing in peace.

### Contact

If you have any questions or comments you can find my contact info on my home page [here](https://brycevandegrift.xyz/).
