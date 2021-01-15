BUILD_DIR=$(shell pwd)
UBOOT_DIR=$(BUILD_DIR)/../u-boot
LINUX_DIR=$(BUILD_DIR)/../linux
BUSYBOX_DIR=$(BUILD_DIR)/../busybox
CROSS_COMPILE=$(BUILD_DIR)/../gcc-arm-none-eabi-10-2020-q4-major/bin/arm-none-eabi-

all: u-boot linux busybox

prepare: prepare-uboot prepare-linux prepare-busybox

prepare-uboot:
	git clone git@github.com:u-boot/u-boot.git $(UBOOT_DIR)
	
prepare-linux:
	git clone git@github.com:torvalds/linux.git $(LINUX_DIR)
	
prepare-busybox:
	git clone git://busybox.net/busybox.git $(BUSYBOX_DIR)

u-boot:
	make -C $(UBOOT_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) smdkc100_defconfig
	make -C $(UBOOT_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE)

u-boot-install:
	sudo dd iflag=sync oflag=sync if=../u-boot/spl/smdkc100-spl.bin of=/dev/mmcblk0 seek=1
	sudo dd iflag=sync oflag=sync if=../u-boot/u-boot.bin of=/dev/mmcblk0 seek=32

u-boot-clean:
	make -C $(UBOOT_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) distclean

linux:
	make -C $(LINUX_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) smdkc100_defconfig
	make -C $(LINUX_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE)
	
linux-clean:
	make -C $(LINUX_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) distclean

busybox:
