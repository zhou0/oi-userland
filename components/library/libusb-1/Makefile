#
# CDDL HEADER START
#
# The contents of this file are subject to the terms of the
# Common Development and Distribution License (the "License").
# You may not use this file except in compliance with the License.
#
# You can obtain a copy of the license at usr/src/OPENSOLARIS.LICENSE
# or http://www.opensolaris.org/os/licensing.
# See the License for the specific language governing permissions
# and limitations under the License.
#
# When distributing Covered Code, include this CDDL HEADER in each
# file and include the License file at usr/src/OPENSOLARIS.LICENSE.
# If applicable, add the following below this CDDL HEADER, with the
# fields enclosed by brackets "[]" replaced with your own identifying
# information: Portions Copyright [yyyy] [name of copyright owner]
#
# CDDL HEADER END

#
# Copyright (c) 2016, Oracle and/or its affiliates. All rights reserved.
#

include ../../../make-rules/shared-macros.mk

COMPONENT_NAME=		libusb
COMPONENT_VERSION=	1.0.20
COMPONENT_REVISION=	1
COMPONENT_SRC=		$(COMPONENT_NAME)-$(COMPONENT_VERSION)
COMPONENT_ARCHIVE=	$(COMPONENT_SRC).tar.bz2
COMPONENT_ARCHIVE_HASH= sha256:cb057190ba0a961768224e4dc6883104c6f945b2bf2ef90d7da39e7c1834f7ff
COMPONENT_ARCHIVE_URL=  http://sourceforge.net/projects/libusb/files/libusb-1.0/$(COMPONENT_SRC)/$(COMPONENT_ARCHIVE)
COMPONENT_PROJECT_URL=  http://libusb.info/
COMPONENT_BUGDB=	library/libusb

TPNO=			26906

include $(WS_MAKE_RULES)/prep.mk
include $(WS_MAKE_RULES)/configure.mk
include $(WS_MAKE_RULES)/ips.mk

CONFIGURE_OPTIONS  +=	--enable-static=no
CONFIGURE_OPTIONS  +=   --enable-log

COMPONENT_BUILD_ARGS+=	"LDFLAGS=$(LDFLAGS)"
COMPONENT_BUILD_ARGS+=	"INSTALL=$(INSTALL)"
COMPONENT_BUILD_ARGS+=	"MAKE=$(GMAKE)"

#ASLR_MODE = $(ASLR_ENABLE)

#Parallel build fails
COMPONENT_BUILD_ARGS=

COMPONENT_PREP_ACTION = (cd $(@D); $(AUTORECONF) -if)

# common targets
configure:	$(CONFIGURE_32_and_64)

build:		$(BUILD_32_and_64)

install:	$(INSTALL_32_and_64)

test:		$(TEST_32_and_64)
