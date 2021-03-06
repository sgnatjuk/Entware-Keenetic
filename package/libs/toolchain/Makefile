#
# Copyright (C) 2007-2014 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# locales package added.

include $(TOPDIR)/rules.mk
PKG_NAME:=toolchain
PKG_RELEASE:=4

ifeq ($(CONFIG_USE_GLIBC),y)
        PKG_RELEASE=5
endif

PKG_MAINTAINER:=Felix Fietkau <nbd@openwrt.org>
PKG_LICENSE:=GPL-3.0-with-GCC-exception

include $(INCLUDE_DIR)/package.mk

ifneq ($(DUMP),1)
  LIBGCC_VERSION:=$(GCC_VERSION)
else
  LIBC_VERSION:=<LIBC_VERSION>
  LIBGCC_VERSION:=<LIBGCC_VERSION>
endif

define Package/gcc/Default
  SECTION:=libs
  CATEGORY:=Base system
  URL:=http://gcc.gnu.org/
  VERSION:=$(LIBGCC_VERSION)-$(PKG_RELEASE)
endef

define Package/locales
$(call Package/gcc/Default)
  TITLE:=Locales support (independent of system lib)
  VERSION:=$(LIBC_VERSION)-$(PKG_RELEASE)
  DEPENDS:=@USE_GLIBC
endef

define Package/locales/config
	menu "Configuration"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_locales

	config TOOLCHAIN_USR_DIR
		string
		prompt "External toolchain usr directory"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_locales

	config LOCALE_PROGS_SPEC
		string
		prompt "programs to work with locales"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_locales
		default "./bin/locale ./bin/localedef"

	config i18N_LOCALES
		string
		prompt "locale sources to install"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_locales

	config i18N_CHARMAPS
		string
		prompt "charmaps to install"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_locales

	endmenu
endef

define Package/locales/postinst
#!/bin/sh

echo "Entware uses separate locale-archive file independent from main system"
if [ -f '/opt/usr/lib/locale/locale-archive' ]
then
	echo "locale-archive file found, checking format"
	/opt/bin/locale.new -a 2>&1 >/dev/null | grep 'LC_COLLATE' > /dev/null
	if [ $$? -eq 0 ] ; then
		echo "Old format locale archive found. Deleting!"
		rm -f /opt/usr/lib/locale/locale-archive
	fi
fi
if ! [ -f '/opt/usr/lib/locale/locale-archive' ]
then
	mkdir -p /opt/usr/lib/locale
	echo "Creating locale archive - /opt/usr/lib/locale/locale-archive"
	echo "Adding en_EN.UTF-8"
	/opt/bin/localedef.new -c -f UTF-8 -i en_US en_US.UTF-8
	echo "Adding ru_RU.UTF-8"
	/opt/bin/localedef.new -c -f UTF-8 -i ru_RU ru_RU.UTF-8
fi
if [ -f '/opt/usr/lib/locale/locale-archive' ]
then
	echo "You can download locale sources from http://pkg.entware.net/sources/i18n_glib223.tar.gz"
	echo "You can add new locales to Entware using /opt/bin/localedef.new"
fi
if ! [ -f '/opt/usr/lib/locale/locale-archive' ]
then
	echo "locale-archive file was not created, not enough memory? Downloading..."
	wget http://pkg.entware.net/binaries/other/locale-archive.2.23 -O /opt/usr/lib/locale/locale-archive
	echo "You can download locale sources from http://pkg.entware.net/sources/i18n_glib223.tar.gz"
	echo "You can add new locales to Entware using /opt/bin/localedef.new"
fi

endef


define Package/libgcc
$(call Package/gcc/Default)
  TITLE:=GCC support library
endef

define Package/libgcc/config
	menu "Configuration"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_libgcc

	config LIBGCC_ROOT_DIR
		string
		prompt "libgcc shared library base directory"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_libgcc
		default TOOLCHAIN_ROOT  if !NATIVE_TOOLCHAIN
		default "/"  if NATIVE_TOOLCHAIN

	config LIBGCC_FILE_SPEC
		string
		prompt "libgcc shared library files (use wildcards)"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_libgcc
		default "./lib/libgcc_s.so.*"

	endmenu
endef

define Package/libatomic
$(call Package/gcc/Default)
  DEPENDS:=+libgcc
  TITLE:=Atomic support library
endef

define Package/libatomic/config
	menu "Configuration"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_libatomic

	config LIBATOMIC_ROOT_DIR
		string
		prompt "libatomic shared library base directory"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_libatomic
		default TOOLCHAIN_ROOT  if !NATIVE_TOOLCHAIN
		default "/"  if NATIVE_TOOLCHAIN

	config LIBATOMIC_FILE_SPEC
		string
		prompt "libatomic shared library files (use wildcards)"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_libatomic
		default "./lib/libatomic.so.*"

	endmenu
endef

define Package/libssp
$(call Package/gcc/Default)
  DEPENDS+=@SSP_SUPPORT
  TITLE:=GCC support library
endef

define Package/libssp/config
	menu "Configuration"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_libssp

	config LIBSSP_ROOT_DIR
		string
		prompt "libssp shared library base directory"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_libssp
		default TOOLCHAIN_ROOT  if !NATIVE_TOOLCHAIN
		default "/"  if NATIVE_TOOLCHAIN

	config LIBSSP_FILE_SPEC
		string
		prompt "libssp shared library files (use wildcards)"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_libssp
		default "./lib/libssp.so.*"

	endmenu
endef


define Package/libstdcpp
$(call Package/gcc/Default)
  NAME:=libstdc++
  TITLE:=GNU Standard C++ Library v3
endef

define Package/libstdcpp/config
	menu "Configuration"
	depends on EXTERNAL_TOOLCHAIN && PACKAGE_libstdcpp

	config LIBSTDCPP_ROOT_DIR
		string
		prompt "libstdcpp shared library base directory"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_libstdcpp
		default TOOLCHAIN_ROOT  if !NATIVE_TOOLCHAIN
		default "/"  if NATIVE_TOOLCHAIN

	config LIBSTDCPP_FILE_SPEC
		string
		prompt "libstdc++ shared library files (use wildcards)"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_libstdcpp
		default "./lib/libstdc++.so.*"

	endmenu
endef


define Package/libc/Default
  SECTION:=libs
  CATEGORY:=Base system
  VERSION:=$(LIBC_VERSION)-$(PKG_RELEASE)
  DEPENDS:=+libgcc
  URL:=$(LIBC_URL)
endef


define Package/libc
$(call Package/libc/Default)
  TITLE:=C library
endef

define Package/libc/config
	menu "Configuration"
	depends on EXTERNAL_TOOLCHAIN && PACKAGE_libc

	config LIBC_ROOT_DIR
		string
		prompt "libc shared library base directory"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_libc
		default TOOLCHAIN_ROOT  if !NATIVE_TOOLCHAIN
		default "/"  if NATIVE_TOOLCHAIN

	config LIBC_FILE_SPEC
		string
		prompt "libc shared library files (use wildcards)"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_libc
		default "./lib/ld{-*.so,-linux*.so.*} ./lib/lib{anl,c,cidn,crypt,dl,m,nsl,nss_dns,nss_files,resolv,util}{-*.so,.so.*}"

	endmenu
endef


define Package/libpthread
$(call Package/libc/Default)
  TITLE:=POSIX thread library
endef

define Package/libpthread/config
	menu "Configuration"
	depends on EXTERNAL_TOOLCHAIN && PACKAGE_libpthread

	config LIBPTHREAD_ROOT_DIR
		string
		prompt "libpthread shared library base directory"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_libpthread
		default TOOLCHAIN_ROOT  if !NATIVE_TOOLCHAIN
		default "/"  if NATIVE_TOOLCHAIN

	config LIBPTHREAD_FILE_SPEC
		string
		prompt "libpthread shared library files (use wildcards)"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_libpthread
		default "./lib/libpthread{-*.so,.so.*}"

	endmenu
endef


define Package/libthread-db
$(call Package/libc/Default)
  DEPENDS:=@!USE_MUSL
  TITLE:=POSIX thread library debugging support
endef

define Package/librt
$(call Package/libc/Default)
  TITLE:=POSIX.1b RealTime extension library
  DEPENDS:=+libpthread
endef

define Package/librt/config
	menu "Configuration"
	depends on EXTERNAL_TOOLCHAIN && PACKAGE_librt

	config LIBRT_ROOT_DIR
		string
		prompt "librt shared library base directory"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_librt
		default TOOLCHAIN_ROOT  if !NATIVE_TOOLCHAIN
		default "/"  if NATIVE_TOOLCHAIN

	config LIBRT_FILE_SPEC
		string
		prompt "librt shared library files (use wildcards)"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_librt
		default "./lib/librt{-*.so,.so.*}"

	endmenu
endef


define Package/libgfortran
$(call Package/gcc/Default)
  TITLE:=GFortran support library
  DEPENDS+=@INSTALL_GFORTRAN
endef

define Package/libgfortran/config
	menu "Configuration"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_libgfortran

	config LIBGFORTRAN_ROOT_DIR
		string
		prompt "libgfortran shared library base directory"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_libgfortran
		default TOOLCHAIN_ROOT  if !NATIVE_TOOLCHAIN
		default "/"  if NATIVE_TOOLCHAIN

	config LIBGFORTRAN_FILE_SPEC
		string
		prompt "libgfortran shared library files (use wildcards)"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_libgfortran
		default "./usr/lib/libgfortran.so.*"

	endmenu
endef


define Package/libgo
$(call Package/gcc/Default)
  TITLE:=Go support library
  DEPENDS+=@INSTALL_GCCGO
endef

define Package/libgo/config
	menu "Configuration"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_libgo

	config LIBGO_ROOT_DIR
		string
		prompt "libgo shared library base directory"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_libgo
		default TOOLCHAIN_ROOT  if !NATIVE_TOOLCHAIN
		default "/"  if NATIVE_TOOLCHAIN

	config LIBGO_FILE_SPEC
		string
		prompt "libgo shared library files (use wildcards)"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_libgo
		default "./usr/lib/libgo.so.*"

	endmenu
endef


define Package/ldd
$(call Package/libc/Default)
  DEPENDS:=@!USE_MUSL
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=LDD trace utility
endef

define Package/ldd/config
	menu "Configuration"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_ldd

	config LDD_ROOT_DIR
		string
		prompt "ldd trace utility base directory"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_ldd
		default TOOLCHAIN_ROOT  if !NATIVE_TOOLCHAIN
		default "/"  if NATIVE_TOOLCHAIN

	config LDD_FILE_SPEC
		string
		prompt "ldd trace utility file"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_ldd
		default "./usr/bin/ldd"

	endmenu
endef


define Package/ldconfig
$(call Package/libc/Default)
  DEPENDS:=@!USE_MUSL
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=Shared library path configuration
endef

define Package/ldconfig/config
	menu "Configuration"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_ldconfig

	config LDCONFIG_ROOT_DIR
		string
		prompt "ldconfig base directory"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_ldconfig
		default TOOLCHAIN_ROOT  if !NATIVE_TOOLCHAIN
		default "/"  if NATIVE_TOOLCHAIN

	config LDCONFIG_FILE_SPEC
		string
		prompt "ldconfig file"
		depends on EXTERNAL_TOOLCHAIN && PACKAGE_ldconfig
		default "./sbin/ldconfig"

	endmenu
endef

define Build/Prepare
	mkdir -p $(PKG_BUILD_DIR)
endef

LIBGCC_A=$(lastword $(wildcard $(TOOLCHAIN_DIR)/lib/gcc/*/*/libgcc_pic.a))
LIBGCC_MAP=$(lastword $(wildcard $(TOOLCHAIN_DIR)/lib/gcc/*/*/libgcc.map))
LIBGCC_SO=$(lastword $(wildcard $(TOOLCHAIN_DIR)/lib/libgcc_s.so.*))
ifeq ($(CONFIG_EXTERNAL_TOOLCHAIN),)
  ifneq ($(if $(CONFIG_USE_UCLIBC),$(CONFIG_GCC_VERSION_LINARO)),)
    BUILD_LIBGCC:=$(if $(CONFIG_m68k)$(CONFIG_powerpc),,$(PKG_BUILD_DIR)/libgcc_s.so.*)
  endif
endif

ifneq ($(BUILD_LIBGCC),)
  define Build/Compile/uClibc
	$(SCRIPT_DIR)/relink-lib.sh \
		"$(TARGET_CROSS)" \
		"$(wildcard $(TOOLCHAIN_DIR)/lib/libc_so.a)" \
		"$(wildcard $(TOOLCHAIN_DIR)/lib/libc_so.a)" \
		"$(patsubst $(TOOLCHAIN_DIR)/lib/%,$(PKG_BUILD_DIR)/%,$(wildcard $(TOOLCHAIN_DIR)/lib/libuClibc-*.so))" \
		-Wl,-init,__uClibc_init -Wl,-soname=libc.so.0 \
		$(BUILD_LIBGCC)
	$(SCRIPT_DIR)/relink-lib.sh \
		"$(TARGET_CROSS)" \
		"$(wildcard $(TOOLCHAIN_DIR)/lib/libcrypt-*.so)" \
		"$(wildcard $(TOOLCHAIN_DIR)/lib/libcrypt_pic.a)" \
		"$(patsubst $(TOOLCHAIN_DIR)/lib/%,$(PKG_BUILD_DIR)/%,$(wildcard $(TOOLCHAIN_DIR)/lib/libcrypt-*.so))" \
		$(BUILD_LIBGCC) \
		-Wl,-soname=libcrypt.so.0
	$(SCRIPT_DIR)/relink-lib.sh \
		"$(TARGET_CROSS)" \
		"$(wildcard $(TOOLCHAIN_DIR)/lib/libatomic.so)" \
		"$(wildcard $(TOOLCHAIN_DIR)/lib/libatomic.a)" \
		"$(patsubst $(TOOLCHAIN_DIR)/lib/%,$(PKG_BUILD_DIR)/%,$(wildcard $(TOOLCHAIN_DIR)/lib/libatomic*.so))" \
		$(BUILD_LIBGCC) \
		-Wl,-soname=libatomic.so.0
	$(SCRIPT_DIR)/relink-lib.sh \
		"$(TARGET_CROSS)" \
		"$(wildcard $(TOOLCHAIN_DIR)/lib/libm-*.so)" \
		"$(wildcard $(TOOLCHAIN_DIR)/lib/libm_pic.a)" \
		"$(patsubst $(TOOLCHAIN_DIR)/lib/%,$(PKG_BUILD_DIR)/%,$(wildcard $(TOOLCHAIN_DIR)/lib/libm-*.so))" \
		$(BUILD_LIBGCC) \
		-Wl,-soname=libm.so.0
	$(SCRIPT_DIR)/relink-lib.sh \
		"$(TARGET_CROSS)" \
		"$(wildcard $(TOOLCHAIN_DIR)/lib/libpthread-*.so)" \
		"$(wildcard $(TOOLCHAIN_DIR)/lib/libpthread_so.a)" \
		"$(patsubst $(TOOLCHAIN_DIR)/lib/%,$(PKG_BUILD_DIR)/%,$(wildcard $(TOOLCHAIN_DIR)/lib/libpthread-*.so))" \
		-Wl,-z,nodelete,-z,initfirst,-init=__pthread_initialize_minimal_internal \
		-ldl -lc $(BUILD_LIBGCC) \
		-Wl,-soname=libpthread.so.0
  endef
  define Build/Compile/libgcc
	$(SCRIPT_DIR)/relink-lib.sh \
		"$(TARGET_CROSS)" \
		"$(LIBGCC_SO)" \
		"$(LIBGCC_A)" \
		"$(patsubst $(TOOLCHAIN_DIR)/lib/%,$(PKG_BUILD_DIR)/%,$(LIBGCC_SO))" \
		-Wl,--version-script=$(LIBGCC_MAP) -Wl,-soname=libgcc_s.so.1
  endef
else
  define Build/Compile/uClibc
	$(CP) \
		$(TOOLCHAIN_DIR)/lib/libuClibc-*.so \
		$(TOOLCHAIN_DIR)/lib/libcrypt-*.so \
		$(TOOLCHAIN_DIR)/lib/libm-*.so \
		$(TOOLCHAIN_DIR)/lib/libpthread-*.so \
		$(PKG_BUILD_DIR)/
  endef
  ifneq ($(LIBGCC_SO),)
    define Build/Compile/libgcc
	$(CP) $(LIBGCC_SO) $(PKG_BUILD_DIR)/
    endef
  endif
endif

define Build/Compile/Default
	$(call Build/Compile/libgcc)
	$(call Build/Compile/$(LIBC))
endef
Build/Compile = $(Build/Compile/Default)

ifeq ($(CONFIG_EXTERNAL_TOOLCHAIN),)

  define Package/libgcc/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(TOOLCHAIN_DIR)/lib/libgcc_s.so.* $(1)/opt/lib/
  endef

  define Package/libatomic/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(TOOLCHAIN_DIR)/lib/libatomic.so.* $(1)/opt/lib/
  endef

  define Package/libgfortran/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(TOOLCHAIN_DIR)/lib/libgfortran.so.* $(1)/opt/lib/
  endef

  define Package/libgo/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(TOOLCHAIN_DIR)/lib/libgo.so.* $(1)/opt/lib/
  endef

  define Package/libssp/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(TOOLCHAIN_DIR)/lib/libssp.so.* $(1)/opt/lib/
  endef

  define Package/libstdcpp/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(TOOLCHAIN_DIR)/lib/libstdc++.so.* $(1)/opt/lib/
  endef

  define Package/glibc/install
	$(INSTALL_DIR) $(1)/opt/etc
	$(CP) ./glibc-files/etc/* $(1)/opt/etc
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) \
		$(TOOLCHAIN_DIR)/lib/ld*.so.* \
		$(TOOLCHAIN_DIR)/lib/ld-$(LIBC_SO_VERSION).so \
		$(TOOLCHAIN_DIR)/lib/libmemusage.so \
		$(TOOLCHAIN_DIR)/lib/libpcprofile.so \
		$(1)/opt/lib/
	for file in libanl libc libcidn libcrypt libdl libm libnsl libnss_dns libnss_files libresolv libutil; do \
		for file in $(TOOLCHAIN_DIR)/lib/$$$$file.so.* $(TOOLCHAIN_DIR)/lib/$$$$file-$(LIBC_SO_VERSION).so; do \
			if [ -e "$$$$file" ]; then \
				$(CP) $$$$file $(1)/opt/lib/; \
			fi; \
		done; \
	done
ifneq ($(CONFIG_TARGET_x86_64),)
	$(CP) $(TOOLCHAIN_DIR)/lib/libmvec-$(LIBC_SO_VERSION).so $(1)/opt/lib
	$(CP) $(TOOLCHAIN_DIR)/lib/libmvec.so.* $(1)/opt/lib
endif
  endef

  define Package/uClibc/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) \
		$(TOOLCHAIN_DIR)/lib/ld*-uClibc.so.* \
		$(TOOLCHAIN_DIR)/lib/ld*-uClibc-$(LIBC_SO_VERSION).so \
		$(1)/opt/lib/
	$(CP) \
		$(TOOLCHAIN_DIR)/lib/libc.so.* \
		$(TOOLCHAIN_DIR)/lib/libuClibc-$(LIBC_SO_VERSION).so \
		$(1)/opt/lib/
	for file in libuargp libcrypt libdl libm libutil libnsl libresolv; do \
		$(CP) \
			$(TOOLCHAIN_DIR)/lib/$$$$file.so* \
			$(TOOLCHAIN_DIR)/lib/$$$$file-$(LIBC_SO_VERSION).so \
			$(1)/opt/lib/; \
	done

	$(CP) \
		$(PKG_BUILD_DIR)/libuClibc-* \
		$(PKG_BUILD_DIR)/libm-* \
		$(PKG_BUILD_DIR)/libcrypt-* \
		$(1)/opt/lib/
  endef

  LD_MUSL_NAME = $(notdir $(firstword $(wildcard $(TOOLCHAIN_DIR)/lib/libc.so*)))

  define Package/musl/install
	$(INSTALL_DIR) $(1)/lib $(1)/usr/bin
	$(CP) \
		$(TOOLCHAIN_DIR)/lib/ld-musl-*.so* \
		$(1)/lib/
	$(CP) \
		$(TOOLCHAIN_DIR)/lib/libc.so* \
		$(1)/lib/
	$(LN) ../../lib/$(LD_MUSL_NAME) $(1)/usr/bin/ldd
  endef

  define Package/libc/install
    $(call Package/$(LIBC)/install,$1)
  endef

  define Package/libc/install_lib
	$(CP) $(filter-out %/libdl_pic.a %/libpthread_pic.a %/libresolv_pic.a,$(wildcard $(TOOLCHAIN_DIR)/lib/lib*.a)) $(1)/opt/lib/
	$(if $(wildcard $(TOOLCHAIN_DIR)/lib/libc_so.a),$(CP) $(TOOLCHAIN_DIR)/lib/libc_so.a $(1)/opt/lib/libc_pic.a)
	$(if $(LIBGCC_MAP), \
		$(CP) $(LIBGCC_A) $(1)/opt/lib/libgcc_s_pic.a; \
		$(CP) $(LIBGCC_MAP) $(1)/opt/lib/libgcc_s_pic.map \
	)
  endef

  define Package/libpthread/install
	$(INSTALL_DIR) $(1)/opt/lib
  ifneq ($(CONFIG_USE_MUSL),y)
	$(CP) \
		$(TOOLCHAIN_DIR)/lib/libpthread.so.* \
		$(if $(BUILD_LIBGCC),\
			$(PKG_BUILD_DIR)/libpthread-$(LIBC_SO_VERSION).so, \
			$(TOOLCHAIN_DIR)/lib/libpthread-$(LIBC_SO_VERSION).so \
		) \
		$(1)/opt/lib/
  endif
  endef

  define Package/libthread-db/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) \
		$(TOOLCHAIN_DIR)/lib/libthread_db.so.* $(1)/opt/lib
	$(CP) \
		$(TOOLCHAIN_DIR)/lib/libthread_db-*.so  $(1)/opt/lib/
  endef

  define Package/libpthread/install_lib
	$(if $(wildcard $(TOOLCHAIN_DIR)/lib/libpthread_so.a),$(CP) $(TOOLCHAIN_DIR)/lib/libpthread_so.a $(1)/opt/lib/libpthread_pic.a)
  endef

  define Package/librt/install
	$(INSTALL_DIR) $(1)/opt/lib
  ifneq ($(CONFIG_USE_MUSL),y)
	$(CP) \
		$(TOOLCHAIN_DIR)/lib/librt.so.* \
		$(TOOLCHAIN_DIR)/lib/librt-$(LIBC_SO_VERSION).so \
		$(1)/opt/lib/
  endif
  endef

  define Package/ldd/install
	$(INSTALL_DIR) $(1)/opt/bin/
	$(CP) $(TOOLCHAIN_DIR)/bin/ldd $(1)/opt/bin/
  ifeq ($(CONFIG_USE_GLIBC),y)
	$(SED) 's|^\#!.*bash|\#!/opt/bin/sh|g; \
	s|/lib/ld-linux.so.3|/opt/lib/ld-linux.so.3|g; \
	s|/lib/ld-linux.so.2|/opt/lib/ld-linux.so.2|g; \
	s|/lib64/ld-linux-x86-64.so.2|/opt/lib/ld-linux-x86-64.so.2|g' $(1)/opt/bin/ldd
  endif
  endef

  define Package/ldconfig/install
	$(INSTALL_DIR) $(1)/opt/sbin/
	$(CP) $(TOOLCHAIN_DIR)/sbin/ldconfig $(1)/opt/sbin/
  endef

ilocales=de_DE en_US en_GB fr_FR it_IT ru_RU i18n iso14651_t1 iso14651_t1_common translit_circle translit_combining translit_font translit_narrow translit_small translit_cjk_compat translit_compat translit_fraction translit_neutral translit_wide
icharmaps=ANSI_X3.4-1968.gz UTF-8.gz



  define Package/locales/install
	$(INSTALL_DIR) $(1)/opt/usr/lib/locale
	$(INSTALL_DIR) $(1)/opt/usr/share/i18n/locales
	$(INSTALL_DIR) $(1)/opt/usr/share/i18n/charmaps
	$(INSTALL_DIR) $(1)/opt/bin
	$(CP) $(TOOLCHAIN_DIR)/bin/locale $(1)/opt/bin/locale.new
	$(CP) $(TOOLCHAIN_DIR)/bin/localedef $(1)/opt/bin/localedef.new
	for file in $(ilocales); do \
		$(CP) $(TOOLCHAIN_DIR)/share/i18n/locales/$$$$file $(1)/opt/usr/share/i18n/locales ; \
	done ; \
	for file in $(icharmaps); do \
		$(CP) $(TOOLCHAIN_DIR)/share/i18n/charmaps/$$$$file $(1)/opt/usr/share/i18n/charmaps ; \
	done ; \
	exit 0
  endef

else

  define Package/libgcc/install
	for file in $(call qstrip,$(CONFIG_LIBGCC_FILE_SPEC)); do \
		dir=`dirname $$$$file` ; \
		$(INSTALL_DIR) $(1)/opt/$$$$dir ; \
		$(CP) $(call qstrip,$(CONFIG_LIBGCC_ROOT_DIR))/$$$$file $(1)/opt/$$$$dir/ ; \
	done ; \
	exit 0
  endef

  define Package/libgfortran/install
	for file in $(call qstrip,$(CONFIG_LIBGFORTRAN_FILE_SPEC)); do \
		dir=`dirname $$$$file` ; \
		$(INSTALL_DIR) $(1)/opt/$$$$dir ; \
		$(CP) $(call qstrip,$(CONFIG_LIBGFORTRAN_ROOT_DIR))/$$$$file $(1)/opt/$$$$dir/ ; \
	done
  endef

  define Package/libgo/install
	for file in $(call qstrip,$(CONFIG_LIBGO_FILE_SPEC)); do \
		dir=`dirname $$$$file` ; \
		$(INSTALL_DIR) $(1)/opt/$$$$dir ; \
		$(CP) $(call qstrip,$(CONFIG_LIBGO_ROOT_DIR))/$$$$file $(1)/opt/$$$$dir/ ; \
	done
  endef

  define Package/libssp/install
	for file in $(call qstrip,$(CONFIG_LIBSSP_FILE_SPEC)); do \
		dir=`dirname $$$$file` ; \
		$(INSTALL_DIR) $(1)/opt/$$$$dir ; \
		$(CP) $(call qstrip,$(CONFIG_LIBSSP_ROOT_DIR))/$$$$file $(1)/opt/$$$$dir/ ; \
	done ; \
	exit 0
  endef

  define Package/libstdcpp/install
	for file in $(call qstrip,$(CONFIG_LIBSTDCPP_FILE_SPEC)); do \
		dir=`dirname $$$$file` ; \
		$(INSTALL_DIR) $(1)/opt/$$$$dir ; \
		$(CP) $(call qstrip,$(CONFIG_LIBSTDCPP_ROOT_DIR))/$$$$file $(1)/opt/$$$$dir/ ; \
	done ; \
	rm -f $(1)/opt/$$$$dir/*.py ; \
	exit 0
  endef

  define Package/libc/install
	for file in $(call qstrip,$(CONFIG_LIBC_FILE_SPEC)); do \
		dir=`dirname $$$$file` ; \
		$(INSTALL_DIR) $(1)/opt/$$$$dir ; \
		$(CP) $(call qstrip,$(CONFIG_LIBC_ROOT_DIR))/$$$$file $(1)/opt/$$$$dir/ ; \
	done ; \
	$(INSTALL_DIR) $(1)/opt/etc ; \
	$(CP) ./glibc-files/etc/nsswitch.conf $(1)/opt/etc/nsswitch.conf ; \
	exit 0
  endef

  define Package/libpthread/install
	for file in $(call qstrip,$(CONFIG_LIBPTHREAD_FILE_SPEC)); do \
		dir=`dirname $$$$file` ; \
		$(INSTALL_DIR) $(1)/opt/$$$$dir ; \
		$(CP) $(call qstrip,$(CONFIG_LIBPTHREAD_ROOT_DIR))/$$$$file $(1)/opt/$$$$dir/ ; \
	done ; \
	exit 0
  endef

  define Package/libthread-db/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(call qstrip,$(CONFIG_LIBPTHREAD_ROOT_DIR))/lib/libthread_db{-*.so,.so.*} $(1)/opt/lib/
  endef

  define Package/librt/install
	for file in $(call qstrip,$(CONFIG_LIBRT_FILE_SPEC)); do \
		dir=`dirname $$$$file` ; \
		$(INSTALL_DIR) $(1)/opt/$$$$dir ; \
		$(CP) $(call qstrip,$(CONFIG_LIBRT_ROOT_DIR))/$$$$file $(1)/opt/$$$$dir/ ; \
	done ; \
	exit 0
  endef

  define Package/libatomic/install
	for file in $(call qstrip,$(CONFIG_LIBATOMIC_FILE_SPEC)); do \
		dir=`dirname $$$$file` ; \
		$(INSTALL_DIR) $(1)/$$$$dir ; \
		$(CP) $(call qstrip,$(CONFIG_LIBATOMIC_ROOT_DIR))/$$$$file $(1)/$$$$dir/ ; \
	done ; \
	exit 0
  endef

  define Package/ldd/install
	for file in $(call qstrip,$(CONFIG_LDD_FILE_SPEC)); do \
		dir=`dirname $$$$file` ; \
		$(INSTALL_DIR) $(1)/opt/bin ; \
		$(CP) $(call qstrip,$(CONFIG_LDD_ROOT_DIR))/$$$$file $(1)/opt/bin/ ; \
	done ; \
	$(SED) 's!/bin/bash!/bin/sh!g' $(1)/opt/bin/ldd ; \
	$(SED) 's!/lib/ld-linux.so.3!/opt/lib/ld-linux.so.3!g' $(1)/opt/bin/ldd ; \
	$(SED) 's!/lib/ld-linux.so.2!/opt/lib/ld-linux.so.2!g' $(1)/opt/bin/ldd ; \
	exit 0
  endef

  define Package/ldconfig/install
	for file in $(call qstrip,$(CONFIG_LDCONFIG_FILE_SPEC)); do \
		dir=`dirname $$$$file` ; \
		$(INSTALL_DIR) $(1)/opt/bin ; \
		$(CP) $(call qstrip,$(CONFIG_LDCONFIG_ROOT_DIR))/$$$$file $(1)/opt/bin/ ; \
	done ; \
	exit 0
  endef

  define Package/locales/install
	$(INSTALL_DIR) $(1)/opt/usr/lib/locale; \
	for file in $(call qstrip,$(CONFIG_LOCALE_PROGS_SPEC)); do \
		dir=`dirname $$$$file` ; \
		$(INSTALL_DIR) $(1)/opt/bin ; \
		$(CP) $(call qstrip,$(CONFIG_TOOLCHAIN_USR_DIR))/$$$$file $(1)/opt/bin/ ; \
	done ; \
	mv $(1)/opt/bin/locale $(1)/opt/bin/locale.new; \
	mv $(1)/opt/bin/localedef $(1)/opt/bin/localedef.new; \
	for file in $(call qstrip,$(CONFIG_i18N_LOCALES)); do \
		dir=`dirname $$$$file` ; \
		$(INSTALL_DIR) $(1)/opt/usr/share/i18n/locales; \
		$(CP) $(call qstrip,$(CONFIG_TOOLCHAIN_USR_DIR))/$$$$file $(1)/opt/usr/share/i18n/locales ; \
	done ; \
	for file in $(call qstrip,$(CONFIG_i18N_CHARMAPS)); do \
		dir=`dirname $$$$file` ; \
		$(INSTALL_DIR) $(1)/opt/usr/share/i18n/charmaps; \
		$(CP) $(call qstrip,$(CONFIG_TOOLCHAIN_USR_DIR))/$$$$file $(1)/opt/usr/share/i18n/charmaps ; \
	done ; \
	exit 0
  endef

endif

$(eval $(call BuildPackage,libc))
$(eval $(call BuildPackage,libgcc))
$(eval $(call BuildPackage,libatomic))
$(eval $(call BuildPackage,libssp))
$(eval $(call BuildPackage,libstdcpp))
$(eval $(call BuildPackage,libpthread))
$(eval $(call BuildPackage,libthread-db))
$(eval $(call BuildPackage,librt))
$(eval $(call BuildPackage,libgfortran))
$(eval $(call BuildPackage,libgo))
$(eval $(call BuildPackage,ldd))
$(eval $(call BuildPackage,ldconfig))
$(eval $(call BuildPackage,locales))
