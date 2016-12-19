include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/version.mk

PKG_NAME:=wifi-board-rt-ac58u
PKG_RELEASE:=1

include $(INCLUDE_DIR)/package.mk

define Package/wifi-board-rt-ac58u
  SECTION:=firmware
  CATEGORY:=Firmware
  DEPENDS:=+ath10k-firmware-qca4019
  TITLE:=Custom Asus' IPQ4019 Boardata for the RT-AC58U/RT-ACRH13
endef

define Package/wifi-board-rt-ac58u/description
This custom board-2.bin overwrites the board-2.bin file,
which is supplied by ath10k-firmware-qca4019. This is
package is only necessary for the ASUS' RT-AC58U/RT-ACRH13.
endef

define Build/Prepare
	mkdir -p $(PKG_BUILD_DIR)
endef

define Build/Compile
endef

define Package/wifi-board-rt-ac58u/install-overlay
	$(INSTALL_DIR) $(1)/lib/firmware/ath10k/QCA4019/hw1.0
	$(CP) ./board-2.bin $(1)/lib/firmware/ath10k/QCA4019/hw1.0
endef

$(eval $(call BuildPackage,wifi-board-rt-ac58u))
