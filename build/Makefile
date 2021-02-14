BUILD_DIR=$(shell pwd)
UBOOT_DIR=$(BUILD_DIR)/../u-boot
LINUX_DIR=$(BUILD_DIR)/../linux
BUSYBOX_DIR=$(BUILD_DIR)/../busybox
ROOTFS_DIR=$(BUILD_DIR)/../rootfs
FSIMG_DIR=$(BUILD_DIR)/../ramdisk
COMPILE_DIR=$(BUILD_DIR)/../gcc-linaro-7.5.0-2019.12-x86_64_arm-linux-gnueabihf
CROSS_COMPILE=$(COMPILE_DIR)/bin/arm-linux-gnueabihf-

SD_DEVICE=/dev/mmcblk0

all: u-boot linux busybox rootfs-install fit_image

clean: u-boot-clean linux-clean busybox-clean rootfs-clean

install: u-boot-install fit-install rootfs-sd

prepare: prepar-gcc prepare-uboot prepare-linux prepare-busybox

prepare-gcc:
	wget -O ../gcc.tar.xz https://releases.linaro.org/components/toolchain/binaries/latest-7/arm-linux-gnueabihf/gcc-linaro-7.5.0-2019.12-x86_64_arm-linux-gnueabihf.tar.xz
	tar -xvf ../gcc.tar.xz -C ../
	rm ../gcc.tar.xz

prepare-uboot:
	git clone git@github.com:u-boot/u-boot.git $(UBOOT_DIR)

prepare-linux:
	git clone git@github.com:torvalds/linux.git $(LINUX_DIR)

prepare-busybox:
	git clone git://busybox.net/busybox.git $(BUSYBOX_DIR)

u-boot:
	make -C $(UBOOT_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) smdkc100_defconfig
	make -C $(UBOOT_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE)
	make -C $(UBOOT_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) envtools

u-boot-install:
	sudo dd iflag=sync oflag=sync if=$(UBOOT_DIR)/spl/smdkc100-spl.bin of=$(SD_DEVICE) seek=1
	sudo dd iflag=sync oflag=sync if=$(UBOOT_DIR)/u-boot.bin of=$(SD_DEVICE) seek=32

u-boot-clean:
	make -C $(UBOOT_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) distclean

linux:
	make -C $(LINUX_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) smart210_defconfig
	make -C $(LINUX_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) dtbs uImage LOADADDR=0x20008000

linux-clean:
	make -C $(LINUX_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) distclean

busybox:
	make -C $(BUSYBOX_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) smart210_defconfig
	make -C $(BUSYBOX_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) install

busybox-clean:
	make -C $(BUSYBOX_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) distclean

rootfs-install:
	cp -ra ../files/rootfs/* ../rootfs/
	mkdir -p ../rootfs/lib ../rootfs/usr/lib
	cp $(COMPILE_DIR)/arm-linux-gnueabihf/libc/lib/*so* ../rootfs/lib/ -d
	cp $(COMPILE_DIR)/arm-linux-gnueabihf/libc/lib/*.a ../rootfs/lib/ -d
	cp $(COMPILE_DIR)/arm-linux-gnueabihf/libc/usr/lib/*so* ../rootfs/usr/lib/ -d
	cp $(COMPILE_DIR)/arm-linux-gnueabihf/libc/usr/lib/*.a ../rootfs/usr/lib/ -d
	cp $(UBOOT_DIR)/tools/env/fw_printenv ../rootfs/usr/bin/ -d
	cp $(UBOOT_DIR)/tools/env/fw_env.config ../rootfs/etc/ -d
	cp ../rootfs/usr/bin/fw_printenv ../rootfs/usr/bin/fw_setenv -d

rootfs-clean:
	rm ../rootfs -rf

rootfs-sd:
	sudo mount -t ext4 /dev/mmcblk0p2 /mnt/SD
	sudo rm -rf /mnt/SD/*
	sudo cp -ra ../rootfs/* /mnt/SD
	sudo umount /mnt/SD
	sudo mount -t ext4 /dev/mmcblk0p3 /mnt/SD
	sudo rm -rf /mnt/SD/*
	sudo cp -ra ../rootfs/* /mnt/SD
	sudo umount /mnt/SD

fit_image:
	${UBOOT_DIR}/tools/mkimage -f ./FitImage.its ./FitImage.itb

fit-install:
	sudo mount -t ext4 /dev/mmcblk0p1 /mnt/SD
	sudo rm -rf /mnt/SD/*
	sudo cp -ra ./FitImage.itb /mnt/SD
	sudo umount /mnt/SD
