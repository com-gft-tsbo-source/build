SHELL := /bin/bash

include ../.makeset
include .makeinfo
-include ../../../../.makeproject
include .makeproject
-include ../../../../.makecommands

TARGET ?= $(PROJECT).$(MODULE).$(COMPONENT)

TIMESTAMP ?= $(shell date +%Y%m%d%H%M%S)
GITHASH ?= $(shell ( git rev-parse HEAD 2>/dev/null || ( $(ECHO) 'unknown' ; exit 0 ) ) ) 
_GITHASH := $(shell ( $(ECHO) "$(GITHASH)" | sed 's/^ *//; s/  *$$//; s/  */\\|/g') )

BUILD_DIR ?= manifests/downloaded/

include .makesrc
ARTIFACT ?= $(BUILD_DIR)/$(COMPONENT).yaml

all: bin

.PHONY: clean docker dist dep lib include distclean bin info _buildinfo _dockerinfo

info: _buildinfo

_buildinfo:
	@$(ECHO) "### KUB /INFO  $(PROJECT).$(MODULE).$(COMPONENT)"
	@$(ECHO) "CUSTOMER       '$(CUSTOMER)'" 
	@$(ECHO) "PROJECT        '$(PROJECT)'" 
	@$(ECHO) "COMPONENT      '$(COMPONENT)'" 
	@$(ECHO) "MODULE         '$(MODULE)'" 
	@$(ECHO) "TIMESTAMP      '$(TIMESTAMP)'" 
	@$(ECHO) "GITHASH        '$(GITHASH)'" 
	@$(ECHO) "ISTIO          '$(ISTIO_VERSION)@$(ISTIO_ARCH)'" 

$(ARTIFACT): 
	@echo "### KUB / DOWN / $(PROJECT).$(MODULE).$(COMPONENT)"
	@$(MKDIR) $(BUILD_DIR)
	@$(CURL) '$(URL)' > $(ARTIFACT).tmp
	@$(MV) $(ARTIFACT).tmp $(ARTIFACT)

clustersetup: $(ARTIFACT)
	@echo "### KUB / KIALI / DEPLOY / $(PROJECT).$(MODULE).$(COMPONENT)"
	@$(KUBECTL) apply -f $(ARTIFACT)

include .makebuild

ls:
	@$(ECHO) "### GOM /LS    $(PROJECT).$(MODULE).$(COMPONENT)"
	@$(LS) -l $(BIN_DIR)/$(TARGET) 2>/dev/null || exit 0
	@($(LS) -l "$(DOCKER_IID)" 2>/dev/null && cat "$(DOCKER_IID)" && $(ECHO) ) ; exit 0

clean:
	@$(ECHO) "### GOM /CLEAN $(PROJECT).$(MODULE).$(COMPONENT)"
#	@$(RM) -f $(ARTIFACT)

distclean: clean clusterclean
	@$(ECHO) "### GOM /DICLN $(PROJECT).$(MODULE).$(COMPONENT)"

all: $(ARTIFACT)


clusterclean clean:
	@echo "### KUB / KIALI / CLEAN - $(PROJECT).$(MODULE).$(COMPONENT)"
	@if [[ -f "manifests/downloaded/prometheus.yaml" ]] ; then kubectl delete -f manifests/downloaded/prometheus.yaml 2>/dev/null || exit 0; fi
	@$(RM) -rf $(BUILD_DIR)
	@$(MKDIR) $(BUILD_DIR)




















# CUSTOMER ?= com.gft.tsbo
# PROJECT ?= com.gft.tsbo.source
# MODULE ?= infrastructure
# COMPONENT ?= kiali
# TARGET ?= $(PROJECT).$(MODULE).$(COMPONENT)
# KUBERNETES_NAMESPACE :=$(shell echo "$PROJECT" | sed 's/[^a-zA-Z0-9_-]/-/g' )

# TIMESTAMP ?= $(shell date +%Y%m%d%H%M%S)
# GITHASH ?= $(shell (  git rev-parse HEAD  ) )
# _GITHASH := $(shell ( echo "$(GITHASH)" | sed 's/^ *//; s/  *$$//; s/  */\\|/g') )

# BUILD_DIR ?= ./build/

# CP ?= cp -pv
# MKDIR ?= mkdir -p
# LN ?= ln
# RM ?= rm
# CURL ?= curl
# KUBECTL ?= kubectl

# all: kiali

# .PHONY: clean kubernetes docker dist dep lib include distclean bin kiali

# kubernetes: \
# 	$(BUILD_DIR)/$(TARGET)-kiali

# kiali: \
# 	$(BUILD_DIR)/$(TARGET)-kiali

# manifests/downloaded/kiali.yaml:
# 	@echo "### KUB / KIALI / DOWN 1 / $(PROJECT).$(MODULE).$(COMPONENT)"
# 	@$(MKDIR) manifests/downloaded/
# 	@$(CURL) 'https://raw.githubusercontent.com/istio/istio/release-$(ISTIO_VERSION)/samples/addons/kiali.yaml' > manifests/downloaded/kiali.yaml

# manifests/downloaded/prometheus.yaml:
# 	@echo "### KUB / KIALI / DOWN 2 / $(PROJECT).$(MODULE).$(COMPONENT)"
# 	@$(MKDIR) manifests/downloaded/
# 	@$(CURL) 'https://raw.githubusercontent.com/istio/istio/release-$(ISTIO_VERSION)/samples/addons/prometheus.yaml' > manifests/downloaded/prometheus.yaml

# manifests/downloaded/jaeger.yaml:
# 	@echo "### KUB / KIALI / DOWN 3 / $(PROJECT).$(MODULE).$(COMPONENT)"
# 	@$(MKDIR) manifests/downloaded/
# 	@$(CURL) 'https://raw.githubusercontent.com/istio/istio/release-$(ISTIO_VERSION)/samples/addons/jaeger.yaml' > manifests/downloaded/jaeger.yaml

# clustersetup $(BUILD_DIR)/$(TARGET)-kiali: manifests/downloaded/kiali.yaml \
# 	                      manifests/downloaded/prometheus.yaml \
# 	                      manifests/downloaded/jaeger.yaml
# 	@echo "### KUB / KIALI / DEPLOY / $(PROJECT).$(MODULE).$(COMPONENT)"
# 	@$(KUBECTL) apply -f manifests/downloaded/kiali.yaml
# 	@$(KUBECTL) apply -f manifests/downloaded/prometheus.yaml
# 	@$(KUBECTL) apply -f manifests/downloaded/jaeger.yaml
# ls:

# docker-ls:

# dist:

# docker: 

# clusterclean clean:
# 	@echo "### KUB / KIALI / CLEAN - $(PROJECT).$(MODULE).$(COMPONENT)"
# 	@if [[ -f "manifests/downloaded/kiali.yaml" ]] ; then kubectl delete -f manifests/downloaded/kiali.yaml 2>/dev/null || exit 0; fi
# 	@if [[ -f "manifests/downloaded/prometheus.yaml" ]] ; then kubectl delete -f manifests/downloaded/prometheus.yaml 2>/dev/null || exit 0; fi
# 	@if [[ -f "manifests/downloaded/jaeger.yaml" ]] ; then kubectl delete -f manifests/downloaded/jaeger.yaml 2>/dev/null || exit 0; fi
# 	@$(RM) -rf $(BUILD_DIR)
# 	@$(MKDIR) $(BUILD_DIR)
# 	@$(MKDIR) manifests/downloaded

# docker-clean:

# distclean:

# docker-distclean: docker-clean

# test:
	
# docker-test:
