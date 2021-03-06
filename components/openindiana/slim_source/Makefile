#
# This file and its contents are supplied under the terms of the
# Common Development and Distribution License ("CDDL)". You may
# only use this file in accordance with the terms of the CDDL.
#
# A full copy of the text of the CDDL should have accompanied this
# source. A copy of the CDDL is also available via the Internet at
# http://www.illumos.org/license/CDDL.
#

#
# Copyright 2013 Adam Stevko. All rights reserved.
# Copyright 2014 Alexander Pyhalov. All rights reserved.
#

include ../../../make-rules/shared-macros.mk

COMPONENT_NAME=		slim_source
COMPONENT_SRC=		$(COMPONENT_NAME)

COMPONENT_REVISION=$(shell cd $(COMPONENT_SRC); git rev-list HEAD --count)

GIT=git
GIT_REPO=https://github.com/OpenIndiana/slim_source.git
GIT_BRANCH=oi/hipster
GIT_CHANGESET=HEAD
NIGHTLY_OPTIONS=-AWndmp +t
LINT=/opt/sunstudio12.1/bin/lint
DMAKE=/usr/bin/dmake
ONNV_BUILDNUM=$(BRANCHID)

PKG_REPO=$(SOURCE_DIR)/packages/$(MACH)/nightly-nd/repo.redist

CLEAN_PATHS += $(BUILD_DIR)

include $(WS_TOP)/make-rules/prep.mk

COMPONENT_ENV = CC=$(CC)
COMPONENT_BUILD_ENV = $(COMPONENT_ENV)
COMPONENT_INSTALL_ENV = $(COMPONENT_ENV)

$(SOURCE_DIR)/.downloaded: $(ARCHIVES:%=$(USERLAND_ARCHIVES)%)
	@[ -d $(SOURCE_DIR) ] || \
	$(GIT) clone -b $(GIT_BRANCH) $(GIT_REPO) $(SOURCE_DIR)
	@cd $(SOURCE_DIR); $(GIT) checkout $(GIT_BRANCH); $(GIT) pull \
	  $(GIT_REPO); $(GIT) log -1 --format=%H > .downloaded

update:
	@[ -d $(SOURCE_DIR) ] || \
	$(GIT) clone -b $(GIT_BRANCH) $(GIT_REPO) $(SOURCE_DIR)
	cd $(SOURCE_DIR); $(GIT) pull $(GIT_REPO); \
	  [ "$$($(GIT) log -1 --format=%H)" == "$$(cat .downloaded)" ] || \
	  $(GIT) log -1 --format=%H > .downloaded

download:: $(SOURCE_DIR)/.downloaded

$(BUILD_DIR)/$(MACH)/.built: $(SOURCE_DIR)/.downloaded Makefile
	$(MKDIR) $(@D) && \
        cd $(SOURCE_DIR) && \
        cat usr/src/tools/env/developer.sh | \
          (sed \
            -e 's|^export NIGHTLY_OPTIONS=.*|export NIGHTLY_OPTIONS=\"$(NIGHTLY_OPTIONS)\"|' \
            -e 's|^export GATE=.*|export GATE=\"$$(git log -1 --format=illumos-%h)\"|' \
            -e 's|^export CODEMGR_WS=.*|export CODEMGR_WS=\"$$PWD\"|'; \
          echo export VERIFY_ELFSIGN=n; \
          echo export CW_NO_SHADOW=1; \
          echo export SPRO_ROOT=; \
          echo export ON_CLOSED_BINS=/tmp; \
          echo export CC=$(CC); \
          echo export LINT=$(LINT); \
          echo export MAKE=$(DMAKE); \
          echo export ONNV_BUILDNUM=$(ONNV_BUILDNUM); \
          echo export PKGVERS_BRANCH=$(ONNV_BUILDNUM)) > \
          developer.sh && \
        time /opt/onbld/bin/nightly developer.sh &&\
	$(TOUCH) $@

build install: $(BUILD_DIR)/$(MACH)/.built

$(BUILD_DIR)/$(MACH)/publish.transforms: $(BUILD_DIR)/$(MACH)/.built
	echo "<transform set name=pkg.fmri -> edit value pkg://[^/]+/ pkg://$(PUBLISHER)/>" > \
	  $(BUILD_DIR)/$(MACH)/publish.transforms
	echo "<transform set name=pkg.fmri -> edit value ,.+: ,$(BUILD_VERSION):>" >> \
	  $(BUILD_DIR)/$(MACH)/publish.transforms
	echo "<transform set name=pkg.fmri -> emit set name=pkg5.info.git-remote value=$(GIT_REPO)>" >> \
	  $(BUILD_DIR)/$(MACH)/publish.transforms
	echo "<transform set name=pkg.fmri -> emit set name=pkg5.info.git-branch value=$(GIT_BRANCH)>" >> \
	  $(BUILD_DIR)/$(MACH)/publish.transforms
	echo "<transform set name=pkg.fmri -> emit set name=pkg5.info.git-rev value=$(shell cd $(COMPONENT_SRC); git rev-parse HEAD)>" >> \
	  $(BUILD_DIR)/$(MACH)/publish.transforms


$(BUILD_DIR)/$(MACH)/.published: $(BUILD_DIR)/$(MACH)/publish.transforms
	if [ -d $(@D)/pkgrecv.dir ]; then $(RM) -r $(@D)/pkgrecv.dir; fi
	$(MKDIR) $(@D)/pkgrecv.dir

	pkgrecv -s $(PKG_REPO) -d $(@D)/pkgrecv.dir --raw \
	  $$(pkgrecv -s $(PKG_REPO) --newest)

	for pkg in $$(echo $(@D)/pkgrecv.dir/*/*); do \
	  pkgmogrify -O $$pkg/manifest $$pkg/manifest \
	  $(BUILD_DIR)/$(MACH)/publish.transforms; \
	  pkgsend -s $(WS_REPO) publish --fmri-in-manifest \
	  -d $(BUILD_DIR)/$(MACH)/overlay -d $$pkg $$pkg/manifest; \
	done

	$(TOUCH) $@

.NOTPARALLEL:

publish: update $(BUILD_DIR)/$(MACH)/.published
