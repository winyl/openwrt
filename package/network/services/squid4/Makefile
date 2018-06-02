#
# Copyright (C) 2006-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=squid4x
PKG_VERSION:=4.0.21
PKG_RELEASE:=1
PKG_BASE_NAME:=squid
PKG_LICENSE:=GPL-2.0
PKG_MAINTAINER:=Sebastian Byczkowski <s.byczkowski@gmail.com>

PKG_SOURCE:=$(PKG_BASE_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=http://www3.us.squid-cache.org/Versions/v4/ \
	http://www2.pl.squid-cache.org/Versions/v4/ \
	http://www.squid-cache.org/Versions/v4/
PKG_HASH:=154d8ebdfdd004f113899b71325dcc401cf07ad8736701032d531c7af1908e33
#PKG_BUILD_DEPENDS:=krb5 heimdal (choose one if in configure)
PKG_BUILD_PARALLEL:=1
PKG_INSTALL:=1

include $(INCLUDE_DIR)/package.mk

define Package/squid4x/Default
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Web Servers/Proxies
  URL:=http://www.squid-cache.org/
  MENU:=1
endef

define Package/squid4x
  $(call Package/squid4x/Default)
  DEPENDS:=+libopenssl +libpthread +librt +libltdl +libstdcpp +libatomic +perl +libbsd
  TITLE:=full-featured Web proxy cache
endef

define Package/squid4x/description
  Squid $(PKG_VERSION) is a caching proxy for the Web supporting HTTP, HTTPS, FTP, and more. It reduces bandwidth and improves response times by caching and reusing frequently-requested web pages.
endef

define Package/squid4x-mod-cachemgr
  $(call Package/squid4x/Default)
  DEPENDS:=squid4x
  TITLE:=Web based proxy manager and reporting tool
endef

define Package/squid4x-mod-cachemgr/description
  Web based proxy manager and reporting tool for Squid4x.
endef

define Package/squid4xclient
  $(call Package/squid4x/Default)
  DEPENDS:=squid4x
  TITLE:=Squidclient and squid purge utility
endef

define Package/squid4xclient/description
  Squidclient and squid purge utility for Squid4x.
endef

CONFIGURE_ARGS += \
	--config-cache \
	--datadir=/usr/share/squid \
	--libexecdir=/usr/lib/squid \
	--sysconfdir=/etc/squid \
	--enable-shared \
	--disable-static \
	--enable-icmp \
	--enable-delay-pools \
	--enable-icap-client \
	--enable-kill-parent-hack \
	--disable-snmp \
	--enable-ssl \
	--enable-ssl-crtd \
	--enable-cache-digests \
	--enable-linux-netfilter \
	--disable-unlinkd \
	--enable-x-accelerator-vary \
	--disable-translation \
	--disable-auto-locale \
	--with-dl \
	--with-pthreads \
	--without-expat \
	--without-libxml2 \
	--without-gnutls \
	--without-nettle \
	--with-openssl=$(STAGING_DIR)/usr \
	--enable-epoll \
	--with-maxfd=4096 \
	--disable-external-acl-helpers \
	--disable-auth-negotiate \
	--disable-auth-ntlm \
	--disable-auth-digest \
	--disable-auth-basic \
	--disable-arch-native \
	--with-krb5-config=no \
	--without-heimdal-krb5 \
	--without-mit-krb5 \
	--without-libcap \
	--with-large-files \
	--without-netfilter-conntrack

CONFIGURE_VARS += \
	ac_cv_header_linux_netfilter_ipv4_h=yes \
	ac_cv_epoll_works=yes \
	squid_cv_gnu_atomics=no

define Build/Compile
	+$(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR)/lib all
	+$(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR) \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		install
endef

define Package/squid4x/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/sbin/squid $(1)/usr/sbin/
	$(SED) "s/^#\!.*/#\!\/usr\/bin\/perl/" $(PKG_INSTALL_DIR)/usr/lib/squid/storeid_file_rewrite
	$(SED) "s/^#\!.*/#\!\/usr\/bin\/perl/" $(PKG_INSTALL_DIR)/usr/lib/squid/helper-mux
	$(SED) "s/^#\!.*/#\!\/usr\/bin\/perl/" $(PKG_INSTALL_DIR)/usr/lib/squid/log_db_daemon
	$(SED) "s/^#\!.*/#\!\/usr\/bin\/perl/" $(PKG_INSTALL_DIR)/usr/lib/squid/security_fake_certverify
	$(SED) "s/^#\!.*/#\!\/usr\/bin\/perl/" $(PKG_INSTALL_DIR)/usr/lib/squid/storeid_file_rewrite
	$(SED) "s/^#\!.*/#\!\/usr\/bin\/perl/" $(PKG_INSTALL_DIR)/usr/lib/squid/url_fake_rewrite.sh
	$(SED) "s/^#\!.*/#\!\/usr\/bin\/perl/" $(PKG_INSTALL_DIR)/usr/lib/squid/url_lfs_rewrite
	$(INSTALL_DIR) $(1)/usr/lib/squid
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/lib/squid/* $(1)/usr/lib/squid/

	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/squid.config $(1)/etc/config/squid

	$(INSTALL_DIR) $(1)/etc/squid
	$(INSTALL_CONF) $(PKG_INSTALL_DIR)/etc/squid/mime.conf $(1)/etc/squid/
	$(INSTALL_CONF) $(PKG_INSTALL_DIR)/etc/squid/errorpage.css $(1)/etc/squid/
	$(INSTALL_CONF) ./files/squid.conf $(1)/etc/squid/

	$(INSTALL_DIR) $(1)/etc/init.d/
	$(INSTALL_BIN) ./files/squid.init $(1)/etc/init.d/squid

	$(INSTALL_DIR) $(1)/usr/share/squid/icons/
	$(CP) $(PKG_INSTALL_DIR)/usr/share/squid/icons/* $(1)/usr/share/squid/icons/

	$(INSTALL_DIR) $(1)/usr/share/squid/errors/templates/
	$(CP) $(PKG_INSTALL_DIR)/usr/share/squid/errors/templates/* $(1)/usr/share/squid/errors/templates/
endef

define Package/squid4x-mod-cachemgr/install
	$(INSTALL_DIR) $(1)/www/cgi-bin/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/squid/cachemgr.cgi $(1)/www/cgi-bin/
endef

define Package/squid4xclient/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/* $(1)/usr/bin/
endef
$(eval $(call BuildPackage,squid4x))
$(eval $(call BuildPackage,squid4x-mod-cachemgr))
$(eval $(call BuildPackage,squid4xclient))
