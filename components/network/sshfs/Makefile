#
# This file and its contents are supplied under the terms of the
# Common Development and Distribution License ("CDDL"). You may
# only use this file in accordance with the terms of the CDDL.
#
# A full copy of the text of the CDDL should have accompanied this
# source. A copy of the CDDL is also available via the Internet at
# http://www.illumos.org/license/CDDL.
#

#
# Copyright 2016 - 2017 Adam Stevko. All rights reserved.
#

include ../../../make-rules/shared-macros.mk

COMPONENT_NAME=		sshfs
COMPONENT_VERSION=	2.8
COMPONENT_FMRI=		network/$(COMPONENT_NAME)
COMPONENT_CLASSIFICATION =	System/Libraries
COMPONENT_SUMMARY=	A network filesystem client to connect to SSH servers
COMPONENT_SRC=		$(COMPONENT_NAME)-$(COMPONENT_VERSION)
COMPONENT_ARCHIVE=	$(COMPONENT_SRC).tar.gz
COMPONENT_ARCHIVE_HASH=	\
	sha256:7f689174d02e6b7e2631306fda4fb8e6b4483102d1bce82b3cdafba33369ad22
COMPONENT_PROJECT_URL=	https://github.com/libfuse/sshfs
COMPONENT_ARCHIVE_URL=	https://github.com/libfuse/sshfs/releases/download/$(COMPONENT_NAME)_$(COMPONENT_VERSION)/$(COMPONENT_ARCHIVE)
COMPONENT_SIG_URL=	https://github.com/libfuse/sshfs/releases/download/$(COMPONENT_NAME)_$(COMPONENT_VERSION)/$(COMPONENT_ARCHIVE).asc
COMPONENT_LICENSE =	GPLv2
COMPONENT_LICENSE_FILE=	COPYING

include $(WS_TOP)/make-rules/prep.mk
include $(WS_TOP)/make-rules/configure.mk
include $(WS_TOP)/make-rules/ips.mk

build:		$(BUILD_32_and_64)

install:	$(INSTALL_32_and_64)

test:		$(TEST_32_and_64)

BUILD_PKG_DEPENDENCIES =	$(BUILD_TOOLS)

include $(WS_TOP)/make-rules/depend.mk

REQUIRED_PACKAGES += library/glib2
REQUIRED_PACKAGES += library/libfuse
REQUIRED_PACKAGES += system/library
