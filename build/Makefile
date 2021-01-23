BUILD_DIR=$(shell pwd)
UBOOT_DIR=$(BUILD_DIR)/../u-boot
LINUX_DIR=$(BUILD_DIR)/../linux
BUSYBOX_DIR=$(BUILD_DIR)/../busybox
ROOTFS_DIR=$(BUILD_DIR)/../rootfs
FSIMG_DIR=$(BUILD_DIR)/../ramdisk
CROSS_COMPILE=$(BUILD_DIR)/../gcc-linaro-7.5.0-2019.12-x86_64_arm-linux-gnueabihf/bin/arm-linux-gnueabihf-

SD_DEVICE=/dev/mmcblk0

all: u-boot linux busybox rootfs

clean: u-boot-clean linux-clean busybox-clean

install: u-boot-install linux-install rootfs-install

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

u-boot-install:
	sudo dd iflag=sync oflag=sync if=$(UBOOT_DIR)/spl/smdkc100-spl.bin of=$(SD_DEVICE) seek=1
	sudo dd iflag=sync oflag=sync if=$(UBOOT_DIR)/u-boot.bin of=$(SD_DEVICE) seek=32

u-boot-clean:
	make -C $(UBOOT_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) distclean

linux:
	make -C $(LINUX_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) smart210_defconfig
	make -C $(LINUX_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) dtbs uImage LOADADDR=0x20008000

linux-install:
	sudo dd iflag=sync oflag=sync if=$(LINUX_DIR)/arch/arm/boot/uImage of=$(SD_DEVICE) seek=1032
	sudo dd iflag=sync oflag=sync if=$(LINUX_DIR)/arch/arm/boot/dts/s5pv210-smdkv210.dtb of=$(SD_DEVICE) seek=17416

linux-clean:
	make -C $(LINUX_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) distclean

busybox:
	make -C $(BUSYBOX_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) smart210_defconfig
	make -C $(BUSYBOX_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) install

busybox-clean:
	make -C $(BUSYBOX_DIR) ARCH=arm CROSS_COMPILE=$(CROSS_COMPILE) distclean

rootfs:
	mkdir -p ${FSIMG_DIR}
	genext2fs -b 4096 -d ${ROOTFS_DIR} ${FSIMG_DIR}/ramdisk
	gzip -9 -f ${FSIMG_DIR}/ramdisk
	${UBOOT_DIR}/tools/mkimage -n smart210 -A arm -O linux -T ramdisk -C gzip -a 0x21000000 -e 0x21000000 -d ${FSIMG_DIR}/ramdisk.gz ${FSIMG_DIR}/ramdisk.img

rootfs-install:
	sudo dd iflag=sync oflag=sync if=${FSIMG_DIR}/ramdisk.img of=$(SD_DEVICE) seek=13320

fit_image:
	${UBOOT_DIR}/tools/mkimage -f ./FitImage.its ./FitImage.itb
