#
# Copyright (C) 2025 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk
include $(INCLUDE_DIR)/netfilter.mk

PKG_NAME:=lunatik
PKG_RELEASE:=3
PKG_LICENSE:=GPL-2.0

include $(INCLUDE_DIR)/package.mk

define KernelPackage/lunatik
  SUBMENU:=Other modules
  TITLE:=Lunatik
  URL:=$PKG_SOURCE_URL
  DEPENDS:= +kmod-nf-ipt #+kmod-compat +kmod-ipt-compat-xtables
  FILES:=$(PKG_BUILD_DIR)/lunatik.ko $(PKG_BUILD_DIR)/lunatik_run.ko \
	 $(PKG_BUILD_DIR)/lib/luarcu.ko $(PKG_BUILD_DIR)/lib/luadevice.ko \
	 $(PKG_BUILD_DIR)/lib/luadata.ko $(PKG_BUILD_DIR)/lib/luanetfilter.ko \
	 $(PKG_BUILD_DIR)/lib/luasocket.ko $(PKG_BUILD_DIR)/lib/luanotifier.ko \
	 $(PKG_BUILD_DIR)/lib/luathread.ko $(PKG_BUILD_DIR)/lib/lualinux.ko
#  AUTOLOAD:=$(call AutoLoad,30,lunatik,1)
  KCONFIG:= CONFIG_NETFILTER=y CONFIG_NETFILTER_ADVANCED=y
endef

define KernelPackage/lunatik/description
  Lunatik ...
endef

ifeq ($(ARCH), x86)
	ifdef CONFIG_X86_32
		KLIBC_ARCH := i386
		asflags-y += -D_REGPARM
	else
		KLIBC_ARCH := x86_64
	endif
else ifeq ($(ARCH), aarch64)
	KLIBC_ARCH := arm64
else
	KLIBC_ARCH := $(ARCH)
endif

KLIBC_USR := /klibc/usr

EXTRA_KCONFIG:= \
	CONFIG_LUNATIK_RUN=m CONFIG_LUNATIK_RUNTIME=y CONFIG_LUNATIK_DEVICE=m   \
	CONFIG_LUNATIK_LINUX=m CONFIG_LUNATIK_NOTIFIER=m CONFIG_LUNATIK_SOCKET=m \
	CONFIG_LUNATIK_RCU=m CONFIG_LUNATIK_THREAD=m CONFIG_LUNATIK_FIB=m \
	CONFIG_LUNATIK_DATA=m CONFIG_LUNATIK_PROBE=m CONFIG_LUNATIK_SYSCALL=m \
	CONFIG_LUNATIK_XDP=m CONFIG_LUNATIK_FIFO=m CONFIG_LUNATIK_NETFILTER=m \
	CONFIG_LUNATIK_COMPLETION=m

EXTRA_CFLAGS:= -D_LUNATIK -D_KERNEL -DLUNATIK_RUNTIME=$(CONFIG_LUNATIK_RUNTIME) \
	-Wimplicit-fallthrough=0 -Wno-declaration-after-statement \
	-I$(PKG_BUILD_DIR) -I$(PKG_BUILD_DIR)/include -I$(PKG_BUILD_DIR)/lua \
	-I$(PKG_BUILD_DIR)$(KLIBC_USR)/include/arch/$(KLIBC_ARCH)

LUA_API:= lua/lua.h lua/lauxlib.h lua/lualib.h

define Build/Prepare
        $(Build/Prepare/Default)
	cd $(PKG_BUILD_DIR) && ./gensymbols.sh $(LUA_API) > lunatik_sym.h
endef

define Build/Compile
	$(KERNEL_MAKE) M="$(PKG_BUILD_DIR)" EXTRA_CFLAGS="$(EXTRA_CFLAGS)" $(EXTRA_KCONFIG) modules
endef

$(eval $(call KernelPackage,lunatik))
