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

#
# Copyright (c) 2011, 2012, Oracle and/or its affiliates. All rights reserved.
#

include ../../../make-rules/shared-macros.mk

COMPONENT_NAME=		graphviz
COMPONENT_VERSION=	2.38.0
COMPONENT_REVISION=	7
COMPONENT_PROJECT_URL=	http://www.graphviz.org/
COMPONENT_SRC=		$(COMPONENT_NAME)-$(COMPONENT_VERSION)
COMPONENT_ARCHIVE=	$(COMPONENT_SRC).tar.gz
COMPONENT_ARCHIVE_HASH=	\
    sha256:81aa238d9d4a010afa73a9d2a704fc3221c731e1e06577c2ab3496bdef67859e
COMPONENT_ARCHIVE_URL=	$(COMPONENT_PROJECT_URL)pub/graphviz/stable/SOURCES/$(COMPONENT_ARCHIVE)
COMPONENT_BUGDB=	utility/graphviz

include $(WS_TOP)/make-rules/prep.mk
include $(WS_TOP)/make-rules/configure.mk
include $(WS_TOP)/make-rules/ips.mk

PATH_PHP.32 = /usr/php/5.6/bin
PATH_PHP.64 = /usr/php/5.6/bin/$(MACH64)
PATH =		/usr/bin:/usr/gnu/bin:$(PATH_PHP.$(BITS))

# GMP header files for the guile extension (no --with- flag)
# The guile build is broken with studio (7073649), so we disable this for now.
# CPPFLAGS +=	-I/usr/include/gmp
# Java header files (--with-javaincludedir allows for only one directory)
CPPFLAGS +=	-I$(JAVA_HOME)/include -I$(JAVA_HOME)/include/solaris
# PHP header files (no --with- flag)
CPPFLAGS_PHP.32 =	$(shell /usr/php/5.6/bin/php-config --includes)
CPPFLAGS_PHP.64 =	$(shell /usr/php/5.6/bin/$(MACH64)/php-config --includes)
CPPFLAGS += $(CPPFLAGS_PHP.$(BITS))
#CXX +=		$(studio_NORUNPATH)

COMPONENT_PREP_ACTION = (cd $(@D) && libtoolize -cf && \
				aclocal && \
				autoconf -f -I m4 && \
				autoheader && \
				automake --add-missing)

# Can't find headers without this.
COMPONENT_PRE_CONFIGURE_ACTION =        ($(CLONEY) $(SOURCE_DIR) $(@D))

CONFIGURE_ENV +=	CPPFLAGS="$(CPPFLAGS)"
CONFIGURE_ENV +=	PERL=$(PERL)
# Explicitly use Ruby 1.9.  Allows switching Ruby default without
# affecting graphviz build.
CONFIGURE_ENV +=	RUBY=$(RUBY)
CONFIGURE_OPTIONS +=	--without-included-ltdl
CONFIGURE_OPTIONS +=	--enable-python=no
CONFIGURE_OPTIONS +=	--enable-python26=no
CONFIGURE_OPTIONS +=	--enable-python27=yes
CONFIGURE_ENV +=	PYTHON27=$(PYTHON.2.7.$(BITS))
# We build only 64-bit java library,
# but avoid building other 64-bit bindings for now
CONFIGURE_OPTIONS.32 +=	--enable-java=no
CONFIGURE_OPTIONS.64 +=	--enable-java=yes
CONFIGURE_OPTIONS.64 +=	--enable-perl=no
CONFIGURE_OPTIONS.64 +=	--enable-ruby=no
CONFIGURE_OPTIONS.64 +=	--enable-tcl=no
CONFIGURE_OPTIONS +=	--x-libraries=$(CONFIGURE_LIBDIR.$(BITS))
CONFIGURE_OPTIONS +=	--with-qt=no

CONFIGURE_ENV.32 += ac_cv_path_GDLIB_CONFIG=/usr/bin/gdlib-config
CONFIGURE_ENV.64 += ac_cv_path_GDLIB_CONFIG=/usr/bin/$(MACH64)/gdlib-config

COMPONENT_BUILD_ARGS += 	GREP=/usr/bin/ggrep

COMPONENT_POST_INSTALL_ACTION += \
    ( cd $(PROTOUSRLIBDIR)/python$(PYTHON_VERSION) ; \
        $(RM) -r vendor-packages-$(BITS) ; \
        $(MV) site-packages vendor-packages-$(BITS) )

# common targets

# We don't want to overwrite 32-bit bits
$(INSTALL_32):	$(INSTALL_64)

build:		$(BUILD_32_and_64)

install:	$(INSTALL_32_and_64)
	env LD_LIBRARY_PATH=$(PROTO_DIR)/usr/lib GVBINDIR=$(PROTO_DIR)/usr/lib/graphviz $(PROTO_DIR)/usr/bin/dot -c
	$(PYTHON.2.7.32) -m compileall $(PROTO_DIR)/usr/lib/graphviz/python27

# The test infrastructure doesn't run in a VPATHed environment.
COMPONENT_PRE_TEST_ACTION = \
	(cd $(@D)/rtest; ln -s $(SOURCE_DIR)/rtest/* .)

# There are tests, but they fail consistently because the test samples are out
# of date and not generated with a known configuration.  See
# http://graphviz.org/bugs/b2063.html
test:		$(NO_TESTS)

BUILD_PKG_DEPENDENCIES =	$(BUILD_TOOLS)

REQUIRED_PACKAGES += image/graphviz
REQUIRED_PACKAGES += image/library/librsvg
REQUIRED_PACKAGES += library/desktop/atk
REQUIRED_PACKAGES += library/desktop/cairo
REQUIRED_PACKAGES += library/desktop/gdk-pixbuf
REQUIRED_PACKAGES += library/desktop/gtk2
REQUIRED_PACKAGES += library/desktop/pango
REQUIRED_PACKAGES += library/expat
REQUIRED_PACKAGES += library/gd
REQUIRED_PACKAGES += library/glib2
REQUIRED_PACKAGES += library/libpoppler
REQUIRED_PACKAGES += library/libtool/libltdl
REQUIRED_PACKAGES += library/math/gts
REQUIRED_PACKAGES += library/zlib
REQUIRED_PACKAGES += print/filter/ghostscript
REQUIRED_PACKAGES += runtime/lua
REQUIRED_PACKAGES += runtime/perl-522
REQUIRED_PACKAGES += runtime/python-27
REQUIRED_PACKAGES += runtime/ruby-23
REQUIRED_PACKAGES += runtime/tk-8
REQUIRED_PACKAGES += system/library
REQUIRED_PACKAGES += system/library/g++-4-runtime
REQUIRED_PACKAGES += system/library/gcc-4-runtime
REQUIRED_PACKAGES += system/library/math
REQUIRED_PACKAGES += x11/library/libx11
REQUIRED_PACKAGES += x11/library/libxmu
REQUIRED_PACKAGES += x11/library/toolkit/libxaw7
REQUIRED_PACKAGES += x11/library/toolkit/libxt
