# Copyright (c) 2014 The Linux Foundation. All rights reserved.
#
include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/image.mk

UBIFS_OPTS = -m 2048 -e 124KiB -c 4096 -U -F

KERNEL_LOADADDR := 0x80208000

define Build/dtb
        $(call Image/BuildDTB,../dts/$(DEVICE_DTS).dts,$@.dtb,,--space $(DTB_SIZE))
endef

define Device/Default
	DEVICE_DTS :=
	DEVICE_DTS_DIR := ../dts
	PROFILES := Default
	KERNEL_SUFFIX := -fit-uImage.itb
	KERNEL_DEPENDS = $$(wildcard $(DTS_DIR)/$$(DEVICE_DTS).dts)
	KERNEL_INITRAMFS_PREFIX := $$(IMG_PREFIX)-$(1)-initramfs
	KERNEL_PREFIX := $$(IMAGE_PREFIX)
	KERNEL_LOADADDR := 0x80208000
endef

define Build/ramdisk
        ( cd $(TARGET_DIR); find . | cpio -o -H newc | gzip -9n > $(KDIR)/root.cpiogz )
endef

define Build/export-dtb
        cp $(IMAGE_KERNEL).dtb $@
endef

# $(1): FIT used kernel compression
# $(2): FIT used ramdisk compression
# $(3): FIT used ramdisk file
define Build/fit-ramdisk
	# create FIT image with and without ramdisk
	./mkits.sh \
		-D $(DEVICE_NAME) -o $@.its -k $@ -d $@.dtb \
		-C $(word 1,$(1)) -a $(KERNEL_LOADADDR) -A $(ARCH) -v $(LINUX_VERSION) \
		-e $(if $(KERNEL_ENTRY),$(KERNEL_ENTRY),$(KERNEL_LOADADDR)) \
		$(if $(word 3,$(1)),-r $(KDIR)/$(word 3,$(1)) -z $(if $(word 2,$(1)),none))
	PATH=$(LINUX_DIR)/scripts/dtc:$(PATH) mkimage -f $@.its $@.new
	@mv $@.new $@
endef

define Build/copy-file
        cat "$(1)" > "$@"
endef


define Device/RT-AC58U
	$(call Device/FitImage)
	DEVICE_DTS := rt-ac58u
	BLOCKSIZE := 128k
	PAGESIZE := 2048
	DTB_SIZE := 65536
	BOARD_NAME := rt-ac58u
	DEVICE_TITLE := Asus RT-AC58U
	IMAGE_SIZE := 20439364
	KERNEL := dtb | kernel-bin | lzma | ramdisk | fit-ramdisk lzma
	KERNEL_INITRAMFS := dtb | kernel-bin | gzip | fit-ramdisk gzip
#	Someone - in their infinite wisdom - decided to put the firmware
#	version in front of the image name \03\00\00\04 => Version 3.0.0.4
#	Since u-boot works with strings we either need another fixup step
#	to add a version... or we are very careful not to add '\0' into that
#	string and call it a day.... Yeah, we do the latter!
	UIMAGE_NAME:=$(shell echo -e '\03\01\01\01RT-AC58U')
	IMAGES = sysupgrade.tar flash-factory.trx
	IMAGE/flash-factory.trx := copy-file $(KDIR)/tmp/$$(KERNEL_INITRAMFS_IMAGE) | uImage none
	IMAGE/sysupgrade.tar := sysupgrade-tar
	DEVICE_PACKAGES := wifi-board-rt-ac58u
endef

TARGET_DEVICES += RT-AC58U

define Build/eva-image
        lzma2eva $(KERNEL_LOADADDR) $(KERNEL_LOADADDR) $@ $@.new
        ( dd if=$@.new bs=64k conv=sync; cat ./eva.dummy.squashfs; ) > $@
endef

define Device/FRITZ4040
	$(call Device/FitImage)
	DEVICE_DTS := fritz4040
	BLOCKSIZE := 128k
	PAGESIZE := 2048
	DTB_SIZE := 65536
	BOARD_NAME := fritz4040
	DEVICE_TITLE := AVM Fritz!Box 4040
	IMAGE_SIZE := 21053440
	KERNEL := kernel-bin | append-dtb | lzma | eva-image
	KERNEL_INITRAMFS := kernel-bin | append-dtb | lzma | eva-image
	IMAGES = sysupgrade.tar
	IMAGE/sysupgrade.tar := sysupgrade-tar
	DEVICE_PACKAGES := wifi-board-fritz4040
endef

TARGET_DEVICES += FRITZ4040

define Device/NBG6617
	$(call Device/FitImage)
	DEVICE_DTS := nbg6617
	BLOCKSIZE := 128k
	PAGESIZE := 2048
	DTB_SIZE := 65536
	BOARD_NAME := nbg6617
	DEVICE_TITLE := ZyXEL NBG6617
	IMAGE_SIZE := 21053440
	KERNEL := dtb | kernel-bin | gzip | fit-ramdisk lzma
	KERNEL_INITRAMFS := dtb | kernel-bin | gzip | fit-ramdisk gzip
	IMAGES = sysupgrade.tar
	IMAGE/sysupgrade.tar := sysupgrade-tar
	DEVICE_PACKAGES := wifi-board-nbg6617
endef

TARGET_DEVICES += NBG6617

$(eval $(call BuildImage))
