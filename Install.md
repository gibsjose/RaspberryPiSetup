#Raspberry Pi OS Installation Instructions
###Author: [gibsjose](www.gibsjose.com) – 2 April 2015

##Introduction
These notes describe the OS installation procedure for the following system:

###Hardware
1. **Raspberry Pi Model B+**
2. **[Edimax EW-7811Un WiFi Adapter](http://www.amazon.com/gp/product/B003MTTJOY/ref=oh_aui_detailpage_o00_s00?ie=UTF8&psc=1)**

###Software
1. **Mac OS X 10.10.x (Yosemite)**
2. **[Raspbian Wheezy Kernel 3.18](http://downloads.raspberrypi.org/raspbian_latest)**

##OS Selection
I selected **Raspbian Wheezy** as my OS because:
1. It is Debian based, and I'm most comfortable with that (yay... `apt-get`)
2. It is the most supported OS currently for the Pi
3. My Pi B+ is not ARMv7, so it can't run Snappy Ubuntu Core :(

The full gamut of *supported* OSes can be found [here](http://www.raspberrypi.org/downloads/).

##Installation Media
I used a 16GB SanDisk SD card as my installation media.

The steps for installing the Raspbian image to the SD card are fairly simple, but there are a few caveats.

1. Mount the SD card (either via a USB adapter or via the actual card slot)
2. Run `diskutil list` to identify the disk number (mine was `disk1`, but yours may be different)
```bash
diskutil list
```
    * **NOTE:** This is the **disk** number (`/dev/disk1`), **NOT** the partition (`/dev/disk1s1`)

3. Unmount the disk with `diskutil unmountDisk`, where `<N>` is the disk number
```bash
diskutil unmountDisk /dev/disk<N>
```

4. Make sure the `.img` file has been extracted from the `.zip`
5. Make the bootable disk with `dd`:
```bash
sudo dd bs=32m if=2015-02-16-raspbian-wheezy.img of=/dev/rdisk1
```
    * **NOTE:** Notice that ***r***disk (that's an 'r' there...) is used. See note below.
    * **NOTE:** The `bs=32m` says to use a block size of 32MB. That is, blocks of size 32MB will be transferred at a time. On some systems, a capital `M`, i.e. (`bs=32M`) must be used, and on some systems it must be lowercase (like in Yosemite). It will complain with an error if you use the wrong case for your system.

***Note:***
It is important to point out the distinction between `/dev/rdisk<N>` and `/dev/disk<N>`. Basically, `rdisk` (which stands for **raw** disk) is quicker to access (sometimes), such as during the next step when we will use `dd`, since it is the *raw disk*, and thus requires less overhead to access than its alias, *disk*, which is 'further' from the actual disk (in INODE hops, sort of).

**The `dd` process should take about ~5 minutes. If it takes longer than 10, there may be something wrong. To check the progress of it, press `CTRL` + `t`, to send a `SIGUSR1` to the `dd` process and make it display info. You can compare the size transferred thus far to the total size of the `.img` file.**

##Boot
Now that we have our installation media, we can plug it into the Pi and then power it on. You should see the **ACT** LED begin to blink, and if it is plugged into a monitor, you should see it begin to boot.
