include $(TOPDIR)/rules.mk

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/iotivity/iotivity.git
PKG_SOURCE_VERSION:=2.0.0
PKG_NAME:=iotivity

PKG_MIRROR_HASH:=whatever

PKG_USE_MIPS16:=0

PKG_BUILD_DEPENDS:=boost

PKG_MAINTAINER:=Bishwajeet Sah <bishwa@cdot.in>

PKG_LICENSE:=Apache-2.0

PKG_CONFIG_DEPENDS := \
	CONFIG_PACKAGE_iotivity-2.0.0 \
	CONFIG_PACKAGE_iotivity-2.0.0_DEBUG \
	CONFIG_PACKAGE_iotivity-2.0.0_SECURE \
	CONFIG_PACKAGE_iotivity-2.0.0_LOGGING \
	CONFIG_PACKAGE_iotivity-2.0.0_WITH_CLOUD

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/scons.mk


define Package/iotivity-2.0.0
  SECTION:=net
  CATEGORY:=Network
  DEPENDS:=@!USE_UCLIBC +libpthread +librt +libuuid +libsqlite3 +libpthread +libstdcpp +boost +boost-system +boost-thread +boost-date_time +libcurl
  TITLE:=IoTivity Libraries
  URL:=https://www.iotivity.org
  MENU:=1
endef

define Package/iotivity-2.0.0/description
    IoTivity is a framework for the Internet of Things based on the Open Interconnect Consortium Specification.
endef

define Package/iotivity-2.0.0/config
  if PACKAGE_iotivity-2.0.0
	config PACKAGE_iotivity-2.0.0_DEBUG
		bool "IoTivity with debug support"
		help
		  Build IoTivity with debuging support.

	config PACKAGE_iotivity-2.0.0_LOGGING
		bool "IoTivity with logging support"
		help
		  Activate logging support.
		  This will make IoTivity write a lot of log messages to stdout.
		  
	config PACKAGE_iotivity-2.0.0_SECURE
		bool "IoTivity with security support"
		default y
		help
		  Activate security support.
	
	config PACKAGE_iotivity-2.0.0_WITH_CLOUD
		bool "IoTivity with cloud support"
		default y
		help
		  Activate cloud support.
		  This will make IoTivity support cloud connectivity.
  endif
endef

define Package/iotivity-2.0.0-resource-samples
  SECTION:=net
  CATEGORY:=Network
  DEPENDS:=+iotivity-2.0.0
  TITLE:=IoTivity Resource Samples
  URL:=https://www.iotivity.org
  MENU:=1
endef

define Package/iotivity-2.0.0-security-tools
  SECTION:=net
  CATEGORY:=Network
  DEPENDS:=+iotivity-2.0.0
  TITLE:=IoTivity Security Tools
  URL:=https://www.iotivity.org
  MENU:=1
endef

PKG_TINYCBOR_NAME:=tinycbor
PKG_TINYCBOR_PROTO:=git
PKG_TINYCBOR_VERSION:=0.5.1
PKG_TINYCBOR_SOURCE_VERSION:=ae64a3d9da39f3bf310b9a7b38427c096d8bcd43
PKG_TINYCBOR_MIRROR_HASH:=whatever
PKG_TINYCBOR_SOURCE:=$(PKG_TINYCBOR_NAME)-$(PKG_TINYCBOR_VERSION).tar.bz2
PKG_TINYCBOR_SOURCE_URL:=https://github.com/intel/tinycbor.git
PKG_TINYCBOR_SUBDIR:=$(PKG_TINYCBOR_NAME)

define Download/openwrtIotivity-tinycbor
  FILE:=$(PKG_TINYCBOR_SOURCE)
  URL:=$(PKG_TINYCBOR_SOURCE_URL)
  MIRROR_HASH:=$(PKG_TINYCBOR_MIRROR_HASH)
  PROTO:=$(PKG_TINYCBOR_PROTO)
  VERSION:=$(PKG_TINYCBOR_SOURCE_VERSION)
  SUBDIR:=$(PKG_TINYCBOR_SUBDIR)
endef
$(eval $(call Download,openwrtIotivity-tinycbor))

PKG_MBEDTLS_NAME:=mbedtls
PKG_MBEDTLS_PROTO:=git
PKG_MBEDTLS_VERSION:=2.4.2
PKG_MBEDTLS_SOURCE_VERSION:=59ae96f167a19f4d04dc6db61f6587b37ccd429f
PKG_MBEDTLS_MIRROR_HASH:=whatever
PKG_MBEDTLS_SOURCE:=$(PKG_MBEDTLS_NAME)-$(PKG_MBEDTLS_VERSION).tar.bz2
PKG_MBEDTLS_SOURCE_URL:=https://github.com/ARMmbed/mbedtls.git
PKG_MBEDTLS_SUBDIR:=$(PKG_MBEDTLS_NAME)

define Download/openwrtIotivity-mbedtls
  FILE:=$(PKG_MBEDTLS_SOURCE)
  URL:=$(PKG_MBEDTLS_SOURCE_URL)
  MIRROR_HASH:=$(PKG_MBEDTLS_MIRROR_HASH)
  PROTO:=$(PKG_MBEDTLS_PROTO)
  VERSION:=$(PKG_MBEDTLS_SOURCE_VERSION)
  SUBDIR:=$(PKG_MBEDTLS_SUBDIR)
endef
$(eval $(call Download,openwrtIotivity-mbedtls))

EXTRA_CXXFLAGS:=-g

SCONS_OPTIONS += \
	TARGET_OS=linux \
	TARGET_TRANSPORT=IP \
	TARGET_ARCH=$(ARCH) \
	STAGING_DIR=$(STAGING_DIR) \
	WITH_ENV=true \
	WITH_TEST=false \
	install
	
ifeq ($(CONFIG_PACKAGE_iotivity-2.0.0_DEBUG),y)
  SCONS_OPTIONS += RELEASE=false
  APP_OPTIM:=debug
else
  SCONS_OPTIONS += RELEASE=true
  APP_OPTIM:=release
endif

ifeq ($(CONFIG_PACKAGE_iotivity-2.0.0_SECURE),y)
  SCONS_OPTIONS += SECURED=1
else
  SCONS_OPTIONS += SECURED=0
endif

ifeq ($(CONFIG_PACKAGE_iotivity-2.0.0_LOGGING),y)
  SCONS_OPTIONS += LOGGING=true
endif

ifeq ($(CONFIG_PACKAGE_iotivity-2.0.0_WITH_CLOUD),y)
  SCONS_OPTIONS += WITH_CLOUD=true
  SCONS_OPTIONS += WITH_TCP=true
endif

ifneq ($(findstring c,$(OPENWRT_VERBOSE)),)
  SCONS_OPTIONS += VERBOSE=true
endif

define Build/Prepare
	$(call Build/Prepare/Default)
	$(TAR) -C $(PKG_BUILD_DIR)/extlibs/tinycbor/ -xjf $(DL_DIR)/$(PKG_TINYCBOR_SOURCE)
	$(TAR) -C $(PKG_BUILD_DIR)/extlibs/mbedtls/ -xjf $(DL_DIR)/$(PKG_MBEDTLS_SOURCE)
endef

define Build/Configure/Default
        (cd $(PKG_BUILD_DIR); \
            $(SCONS_VARS) \
            scons \
		        --prefix=$(STAGING_DIR)/usr \
		        $(SCONS_OPTIONS) \
        )
endef

define Package/iotivity-2.0.0/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/libconnectivity_abstraction.so $(1)/usr/lib/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/liboc.so $(1)/usr/lib/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/liboctbstack.so $(1)/usr/lib/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/liboc_logger.so $(1)/usr/lib/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/libocprovision.so $(1)/usr/lib/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/libocpmapi.so $(1)/usr/lib/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/libnotification_consumer.so $(1)/usr/lib/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/libnotification_provider.so $(1)/usr/lib/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/libcoap_http_proxy.so $(1)/usr/lib/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/libESMediatorRich.so $(1)/usr/lib/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/librcs_common.so $(1)/usr/lib/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/librcs_container.so $(1)/usr/lib/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/libresource_directory.so $(1)/usr/lib/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/librcs_server.so $(1)/usr/lib/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/librcs_client.so $(1)/usr/lib/
endef

define Package/iotivity-2.0.0-security-tools/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/csdk/security/tool/json2cbor $(1)/usr/bin/json2cbor
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/csdk/security/tool/svrdbeditor $(1)/usr/bin/svrdbeditor
endef

define Package/iotivity-2.0.0-resource-samples/install
	$(INSTALL_DIR) $(1)/usr/iotivity-samples/resource
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/simpleclient $(1)/usr/iotivity-samples/resource/simpleclient
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/simpleserver $(1)/usr/iotivity-samples/resource/simpleserver
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/oic_svr_db_client.dat $(1)/usr/iotivity-samples/resource/oic_svr_db_client.dat
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/oic_svr_db_client.json $(1)/usr/iotivity-samples/resource/oic_svr_db_client.json
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/oic_svr_db_server.dat $(1)/usr/iotivity-samples/resource/oic_svr_db_server.dat
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/oic_svr_db_server.json $(1)/usr/iotivity-samples/resource/oic_svr_db_server.json
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/simpleserver_introspection.dat $(1)/usr/iotivity-samples/resource/simpleserver_introspection.dat
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/simpleserver_introspection.json $(1)/usr/iotivity-samples/resource/simpleserver_introspection.json	
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/devicediscoveryclient $(1)/usr/iotivity-samples/resource/devicediscoveryclient
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/devicediscoveryserver $(1)/usr/iotivity-samples/resource/devicediscoveryserver
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/fridgeclient $(1)/usr/iotivity-samples/resource/fridgeclient
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/fridgeserver $(1)/usr/iotivity-samples/resource/fridgeserver
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/garageclient $(1)/usr/iotivity-samples/resource/garageclient
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/garageserver $(1)/usr/iotivity-samples/resource/garageserver
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/groupclient $(1)/usr/iotivity-samples/resource/groupclient
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/groupserver $(1)/usr/iotivity-samples/resource/groupserver
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/lightserver $(1)/usr/iotivity-samples/resource/lightserver
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/light_introspection.dat $(1)/usr/iotivity-samples/resource/light_introspection.dat
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/light_introspection.json $(1)/usr/iotivity-samples/resource/light_introspection.json
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/presenceclient $(1)/usr/iotivity-samples/resource/presenceclient
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/presenceserver $(1)/usr/iotivity-samples/resource/presenceserver
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/rdclient $(1)/usr/iotivity-samples/resource/rdclient
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/roomclient $(1)/usr/iotivity-samples/resource/roomclient
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/roomserver $(1)/usr/iotivity-samples/resource/roomserver
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/simpleclientHQ $(1)/usr/iotivity-samples/resource/simpleclientHQ
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/simpleserverHQ $(1)/usr/iotivity-samples/resource/simpleserverHQ
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/simpleclientserver $(1)/usr/iotivity-samples/resource/simpleclientserver
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/out/linux/$(ARCH)/$(APP_OPTIM)/resource/examples/threadingsample $(1)/usr/iotivity-samples/resource/threadingsampl
endef

$(eval $(call BuildPackage,iotivity-2.0.0))
$(eval $(call BuildPackage,iotivity-2.0.0-security-tools))
$(eval $(call BuildPackage,iotivity-2.0.0-resource-samples))
