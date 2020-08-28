<h1> The First Stage: Discuss About U-boot Source</h1>

### Step 1: Download U-boot source code
```text
  ftp://ftp.denx.de/pub/u-boot/
```

### Step 2: Extract file and discuss about the U-boot architecture
```shell
  tar -xf u-boot-2017.05-rc2.tar.bz2
```
- Different processor architecture produced by ARM
```text
arch
├── arm
    ├── cpu
        ├── arm11
        ├── arm1136
        ├── arm1176
        ├── arm720t
        ├── arm920t
        ├── arm926ejs
        ├── arm946es
        ├── armv7
        ├── armv7m
        ├── armv8
        ├── pxa
        └── sa1100
```
- U-boot supports various boards such as TI with AM355x
```text
board
├── ti
    ├── am335x
    ├── am3517crane
    ├── am43xx
    ├── am57xx
    ├── am65x
    ├── beagle
    ├── common
    ├── dra7xx
    ├── evm
    ├── j721e
    ├── ks2_evm
    ├── omap5_uevm
    ├── panda
    ├── sdp4430
    └── ti816x
```
- And default configuration is used when you have no idea how to configure the U-Boot source code.
  It is guaranteed that the basic and the required functionalities
```text
 /configs/am335x_boneblack_defconfig
````

-----------------------------

<h1> The Second Stage: How To Create MLO/SPL, u-boot.img,..etc </h1>

## Cross toolchain compile install (skip if you using Ubuntu, Debian,..)

If you want to use toolchain from source file
### Step 1: Download arm cross toolchain
```text
  https://releases.linaro.org/components/toolchain/binaries/6.3-2017.02/arm-linux-gnueabihf/
```
### Step 2: Export path of the cross compilation toolchain
```shell 
  export PATH=$PATH:/<include_path>/6.3-2017.02/arm-linux-gnueabihf/bin
```
or install by command
```shell
  sudo apt-get install arm-linux-gnueabihf-*
```


## U-Boot compilation

### Step 1: Delete all the previously compiled/generated object files
```shell
  make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- distclean
```

### Step 2: Apply board default configuration for uboot
```shell
  make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- am335x_boneblack_defconfig
```

### Step 3: If you want to do any settings other than default configuration
```shell
  make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- menuconfig
```

### Step 4: Compile
   You can find number of cores your CPU have
```shell
  cat /proc/cpuinfo | grep processor | wc -l
```

```shell
  make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- -j4

  make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- -j8
```
- j4 (4 core machine) will instructs the make tool to spawn 4 threads
- j8 (8 core machine) will instructs the make tool to spawn 8 threads
![Screenshot from 2020-08-27 23-10-06](https://user-images.githubusercontent.com/32474027/91456152-e6640900-e8bd-11ea-9c79-ace5e37ef8bb.png)

**And here is my result including MLO, u-boot.img, etc:**

![Screenshot from 2020-08-27 23-23-58](https://user-images.githubusercontent.com/32474027/91456251-ff6cba00-e8bd-11ea-8128-b7e78e4cd0c7.png)

-------------------------------------------
<h1> The Third Stage: Generate Linux Image </h1>

### Step 1: Download Linux Kernel source from official BeagleBoard:
```shell
  git clone https://github.com/beagleboard/linux.git
```

### Step 2: Discuss about source file

- Linux supports running on these various different processor architectures
```text
   arch
    ├── alpha
    ├── arc
    ├── arm
    ├── arm64
    ├── avr32
    ├── blackfin
    ├── c6x
    ├── cris
    ├── frv
    ├── h8300
    ├── hexagon
    ├── ia64
    ├── m32r
    ├── m68k
    ├── metag
    ├── microblaze
    ├── mips
    ├── mn10300
    ├── nios2
    ├── openrisc
    ├── parisc
    ├── powerpc
    ├── s390
    ├── score
    ├── sh
    ├── sparc
    ├── tile
    ├── um
    ├── unicore32
    ├── x86
    └── xtensa
```

- Basically every vendor of the SOC has their own machine directory to keep their SOC specific source codes
```tetx
  arch
  └── arm
      ├── mach-alpine               AlphaScale ASM9260
      ├── mach-asm9260              Microchip's(ATMEL) ARM-based SAM9260
      ├── mach-at91
      ├── mach-axxia
      ├── mach-bcm                  Boardcom
      ├── mach-berlin
      ├── mach-clps711x
      ├── mach-cns3xxx
      ├── mach-davinci              Texas Instruments DaVinci
      ├── mach-digicolor
      ├── mach-dove
      ├── mach-ebsa110
      ├── mach-efm32
      ├── mach-ep93xx
      ├── mach-exynos
      ├── mach-footbridge
      ├── mach-gemini
      ├── mach-highbank
      ├── mach-hisi
      ├── mach-imx
      ├── mach-integrator
      ├── mach-iop13xx
      ├── mach-iop32x
      ├── mach-iop33x
      ├── mach-ixp4xx
      ├── mach-keystone
      ├── mach-ks8695               Micrel Semiconductor
      ├── mach-lpc18xx              NXP LPC18xx ARM Cortex-M3 Microcontroller
      ├── mach-lpc32xx              NXP ARM926EJ-S CPU core based SOCs
      ├── mach-mediatek             Mediatek Cortex-A7 based SOCs
      ├── mach-meson
      ├── mach-mmp
      ├── mach-moxart
      ├── mach-mv78xx0
      ├── mach-mvebu
      ├── mach-mxs
      ├── mach-netx
      ├── mach-nomadik
      ├── mach-nspire
      ├── mach-omap1
      ├── mach-omap2                Texas Instrument OMAP2/3/4, AM33xx SOC
      ├── mach-orion5x
      ├── mach-picoxcell
      ├── mach-prima2
      ├── mach-pxa
      ├── mach-qcom                 Qualcomm
      └── ...    
```

- Soc specific driver
```text
  Touchscreen:                   drivers
                                  └── input
                                      └── touchscreen
                                           └── ti_am335x_tsc.c

  Lcd:                           drivers
                                  └── video
                                      └── fbdev
                      
  MMC/SD/SDIO:                   drivers
                                  └── mmc
                                      └── host
                                          └──omap_hsmmc.c

  SPI:                           drivers
                                  └── spi
                                      └── spi-omap2-mcspi.c
 
  I2C:                           drivers
                                  └── i2c
                                      └── busses
                                          └──i2c-omap.c
  
  UART:                          drivers
                                  └── tty
                                      └── serial
                                          └── omap-serial.c
  
  NAND/NOR - External RAM:       drivers
                                  └── memory
                                      └── omap-gpmc.c
                                     
  USB:                           drivers
                                  └── usb
                                      └── musb
                                           ├── musb-core.c
                                           ├── musb_gadget.c
                                           └── musb_host.c
                                 drivers
                                  └── usb
                                      └── phy
                                           └── phy-am335x.c

  Watchdog:                      drivers
                                  └── watchdog
                                      └── omap_wdt.c
  
  RTC:                           drivers
                                  └── rtc
                                      └── rtc-omap.c
```

### Step 3: Delete all the previously compiled/generated object files
```shell
  make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- distclean
```

### Step 4: Apply board default configuration for uboot
```shell
  make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- bb.org_defconfig
```

### Step 5: If you want to do any settings other than default configuration
You can enable/disable driver module,..etc
```shell
  make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- menuconfig
```
  **Note:**
  - "*" static loadable kernel modules
  - "M" dynamic loadable kernel modules

### Step 6: Mention the output name of the kernel image, address in the RAM
```shell
  make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- uImage dtbs LOADADDR=0x80008000 -j4
```
**after generated**

![Screenshot from 2020-08-29 00-21-00](https://user-images.githubusercontent.com/32474027/91584800-24c8f900-e98e-11ea-8a9d-6f5c0f36e04d.png)

If you have problem such as:
```text
  /bin/sh: 1: lzop: not found
  arch/arm/boot/compressed/Makefile:178: recipe for target 'arch/arm/boot/compressed/piggy.lzo' failed
  make[2]: *** [arch/arm/boot/compressed/piggy.lzo] Error 1
````
You must install lzop by
```shell
  sudo apt-get -y install lzop
```
### Step 7: Compile and generate dynamic loadable kernel modules with the extension .ko
The dynamic loadable kernel modules are not yet compiled "M entries", you to compile them separately.
```shell
  make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- modules -j4
```
### Step 8: Kernel modules install
All those generated modules (step7) `*`.ko files should be tranfer to the [RFS (root file system)](https://github.com/nghiaphamsg/BeagleboneBlack_Debian/tree/master/Busybox) 
```shell
~busybox-1.32.0$ make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- INSTALL_MOD_PATH=<install_path> modules_install
```
**Atfer generated and now discuss directory**
- File list out all the static loadable
```text
  lib/modules/4.4.155/modules.builtin
```
- File list out all the dynamically loadable
```text
  lib/modules/4.4.155/modules.dep
```

### See more questions:
1. [What is different between u-boot.bin and u-boot.img ?](https://stackoverflow.com/questions/29494321/what-is-different-between-u-boot-bin-and-u-boot-img)
2. [Can anyone explain the gcc cross-compiler naming convention?](https://stackoverflow.com/questions/5731495/can-anyone-explain-the-gcc-cross-compiler-naming-convention)

















