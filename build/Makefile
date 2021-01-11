BUILD_DIR=$(shell pwd)
UBOOT_DIR=$(BUILD_DIR)/../u-boot
LINUX_DIR=$(BUILD_DIR)/../linux
CROSS_COMPILE=$(BUILD_DIR)/../gcc-arm-none-eabi-10-2020-q4-major/bin/arm-none-eabi-

all: u-boot linux busybox

u-boot:
	make -C $(UBOOT_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) smdkc100_defconfig
	make -C $(UBOOT_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE)

u-boot-clean:
	make -C $(UBOOT_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) distclean

linux:
	make -C $(LINUX_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) smdkc100_defconfig
	make -C $(LINUX_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE)
	
linux-clean:
	make -C $(LINUX_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) distclean

busybox:
