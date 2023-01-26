SHELL := /bin/bash

include ../../../.makeinfo
include ../../../.makeproject
include ../../../.makecommands
include .makeset

TARGET ?= $(PROJECT).$(MODULE).$(COMPONENT)

TIMESTAMP ?= $(shell date +%Y%m%d%H%M%S)
GITHASH ?= $(shell ( git rev-parse HEAD 2>/dev/null || ( $(ECHO) 'unknown' ; exit 0 ) ) ) 
_GITHASH := $(shell ( $(ECHO) "$(GITHASH)" | sed 's/^ *//; s/  *$$//; s/  */\\|/g') )

MODULES := $(shell find . -maxdepth 2 -mindepth 2 -type f -name Makefile | sed 's%/Makefile%%; s%^\./%%' )

.PHONY: all old new $(MODULES) clean distclean docker info _buildinfo

all: $(MODULES)

_buildinfo:
	@$(ECHO) "### KUB /INFO  $(PROJECT)"
	@$(ECHO) "MODULES        $(MODULES)"
	@$(ECHO) "CUSTOMER       '$(CUSTOMER)'" 
	@$(ECHO) "PROJECT        '$(PROJECT)'" 
	@$(ECHO) "COMPONENT      '$(COMPONENT)'" 
	@$(ECHO) "MODULE         '$(MODULE)'" 
	@$(ECHO) "TIMESTAMP      '$(TIMESTAMP)'" 
	@$(ECHO) "GITHASH        '$(GITHASH)'" 

$(MODULES):
	@$(MAKE) -C $@ $(DOCKER_TARGET)

info: _buildinfo
	@for i in $(MODULES) ; do \
	    if [[ ! -d "$$i" ]] ; then continue ; fi ; \
			make -C "$$i" info ; \
	done

clean distclean ls:
	@for i in $(MODULES) ; do \
	    if [[ ! -d "$$i" ]] ; then continue ; fi ; \
			$(MAKE) -C "$$i" $@ ; \
	done
