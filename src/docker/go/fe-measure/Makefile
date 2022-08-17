SHELL := /bin/bash

include .makeinfo
include .makeproject

TARGET ?= $(PROJECT).$(MODULE).$(COMPONENT)

CP ?= cp -pv
CAT ?= cat
MKDIR ?= mkdir -p
LN ?= ln
RM ?= rm
ECHO ?= echo
PRINTF ?= printf
DOCKER ?= docker

TIMESTAMP ?= $(shell date +%Y%m%d%H%M%S)
GITHASH ?= $(shell ( git rev-parse HEAD 2>/dev/null || ( $(ECHO) 'unknown' ; exit 0 ) ) ) 
_GITHASH := $(shell ( $(ECHO) "$(GITHASH)" | sed 's/^ *//; s/  *$$//; s/  */\\|/g') )

BUILD_DIR ?= build

PATH_BASE         ?= ../../../$(SOURCE_TYPE)/$(COMPONENT)
DOCKER_DIR        ?= $(BUILD_DIR)/docker
DOCKER_VARIANT    ?= alpine
DOCKER_SUFFIXES   := v1 v2 v3
DOCKER_FILES      := $(addprefix Dockerfile-, $(DOCKER_SUFFIXES))
DOCKER_IMAGE_BASE := $(TARGET):base
DOCKER_IMAGES     := $(addprefix $(TARGET):, $(DOCKER_SUFFIXES))
DOCKER_IID_BASE   := $(abspath $(DOCKER_DIR)/$(TARGET)-base-$(DOCKER_VARIANT)).iid
DOCKER_IIDS       := $(addprefix $(DOCKER_DIR)/$(TARGET)-, $(addsuffix .iid,$(DOCKER_SUFFIXES)))
DOCKER_REMOTE     ?= xxx.localhost

include .makesrc

all: docker

.PHONY: docker-clean clean docker-distclean distclean docker docker-ls ls docker-test test push docker-push dummy

docker: $(DOCKER_IID_BASE) $(DOCKER_IIDS) $(DOCKER_FILES)
docker-push: push

push: $(DOCKER_IID_BASE) $(DOCKER_IIDS)
	@for f in $? ; do \
    i=$$( sed 's/^[^:]*://' "$$f" ) && \
    $(DOCKER) image tag "$$i" "$(DOCKER_REMOTE)/$(TARGET):base" && \
    $(DOCKER) image push "$(DOCKER_REMOTE)/$(TARGET):base" ; \
  done

$(DOCKER_IID_BASE):
	@TIMESTAMP="$(TIMESTAMP)" \
 	CUSTOMER="$(CUSTOMER)" \
 	PROJECT="$(PROJECT)" \
 	MODULE="$(MODULE)" \
 	COMPONENT="$(COMPONENT)" \
 	DOCKER_IMAGE="$(DOCKER_IMAGE_BASE)" \
 	DOCKER_IID="$(DOCKER_IID_BASE)" \
 	make -C  $(PATH_BASE) docker

$(DOCKER_DIR)/$(TARGET)-v%.iid: VERSION=$*.0.0

$(DOCKER_DIR)/$(TARGET)-%.iid: Dockerfile-% \
	$(SRCS) 
	@$(ECHO) "### DOCK/DOCK  $(PROJECT).$(MODULE).$(COMPONENT) - $(DOCKER_VARIANT) - $*"
	@$(MKDIR) "$(DOCKER_DIR)"
	@if [ -f "$@" ] ; then i=$$( $(CAT) "$@" ); $(DOCKER) image rm -f $$i ; rm -f "$@"  2>/dev/null ; fi
	$(DOCKER) image build -f $< \
	  --build-arg GITHASH="$(_GITHASH)" \
	  --build-arg COMPONENT=$(COMPONENT) \
	  --build-arg MODULE=$(MODULE) \
	  --build-arg PROJECT=$(PROJECT) \
	  --build-arg CUSTOMER=$(CUSTOMER) \
	  --build-arg VERSION=$(VERSION) \
	  --tag $(TARGET):$* \
	  --label GITHASH="$(_GITHASH)" \
	  --label COMPONENT=$(COMPONENT) \
	  --label MODULE=$(MODULE) \
	  --label PROJECT=$(PROJECT) \
	  --label CUSTOMER=$(CUSTOMER) \
	  --label VERSION=$(VERSION) \
	  --iidfile "$@" \
	  .

Dockerfile-%: tmpl/Dockerfile-%.tmpl .makeinfo .makeproject
	@sed "\
     s/{{CUSTOMER}}/$(CUSTOMER)/g; \
     s/{{PROJECT}}/$(PROJECT)/g; \
     s/{{MODULE}}/$(MODULE)/g; \
     s/{{COMPONENT}}/$(COMPONENT)/g; \
     s/{{TARGET}}/$(TARGET)/g; \
     s/{{GITHASH}}/$(GITHASH)/g; \
     s/{{DOCKER_VARIANT}}/$(DOCKER_VARIANT)/g; \
     s/{{DOCKER_NR}}/$*/g; \
     " $< > $@

docker-clean clean:
	@$(ECHO) "### DOCK/DOCLN $(PROJECT).$(MODULE).$(COMPONENT) - $(DOCKER_VARIANT)"
	@while read img imgname ; do \
	    while read id state name image ; do \
	        $(PRINTF) 'C %-7s %-10s %-20s %s\n' "$$id" "$$state" "$$name" "$$image" ; \
	        $(DOCKER) container stop --time 5 "$$id" ; \
	    done < <( $(DOCKER) container ls --filter "ancestor=$$img" --format='{{.ID}} {{.State}} {{.Names}} {{.Image}}'  | sort -u ) ; \
	    $(ECHO) "I $$img $$imgname" ; \
	    $(DOCKER) image rm -f $$img ; \
	done < <(for v in $(DOCKER_SUFFIXES) base ; do $(DOCKER) image ls --filter "label=PROJECT=$(PROJECT)" --filter "label=COMPONENT=$(COMPONENT)" --filter "label=MODULE=$(MODULE)" --filter "label=CUSTOMER=$(CUSTOMER)" --format='{{.ID}} {{.Repository}}:{{.Tag}}' "$(TARGET):$$v" ; done | sort -k 2 -u ) 
	@for v in $(DOCKER_IIDS) $(DOCKER_IID_BASE) ; do \
	    if [ -f "$$v" ] ; then \
	        i=$$( $(CAT) "$$v" ) ; \
	        $(DOCKER) image rm -f $$i 2>/dev/null ; \
	        rm -f "$$v"  2>/dev/null ; \
	    fi ; \
	done

docker-distclean distclean: clean
	@$(ECHO) "### DOCK/DOCLN $(PROJECT).$(MODULE).$(COMPONENT) - $(DOCKER_VARIANT)"
	@while read img imgname ; do \
		$(ECHO) "I $$img $$imgname" ; \
		while read id state name image ; do \
			$(PRINTF) 'C %-7s %-10s %-20s %s\n' "$$id" "$$state" "$$name" "$$image" ; \
		done < <( $(DOCKER) container ls --filter "ancestor=$$img" --format='{{.ID}} {{.State}} {{.Names}} {{.Image}}'  | sort -u ) ; \
	done < <($(DOCKER) image ls --filter "label=PROJECT=$(PROJECT)" --filter "label=COMPONENT=$(COMPONENT)" --format='{{.ID}} {{.Repository}}:{{.Tag}}' | sort -k 2 -u)
	@while read img imgname ; do \
	    $(ECHO) "I $$img $$imgname" ; \
	    $(DOCKER) image rm -f $$img ; \
	done < <($(DOCKER) image ls --filter "label=PROJECT=$(PROJECT)" --filter "label=COMPONENT=$(COMPONENT)" --format='{{.ID}} {{.Repository}}:{{.Tag}}' | sort -k 2 -u)
	@rm -f $(DOCKER_IID_FILES) $(DOCKER_IID_BASE)


docker-ls ls:
	@$(ECHO) "### DOCK/DOLS  $(PROJECT).$(MODULE).$(COMPONENT) - $(DOCKER_VARIANT)"
	@while read img imgname ; do \
		$(ECHO) "I $$img $$imgname" ; \
		while read id state name image ; do \
			$(PRINTF) 'C %-7s %-10s %-20s %s\n' "$$id" "$$state" "$$name" "$$image" ; \
		done < <( $(DOCKER) container ls --filter "ancestor=$$img" --format='{{.ID}} {{.State}} {{.Names}} {{.Image}}'  | sort -u ) ; \
	done < <($(DOCKER) image ls --filter "label=PROJECT=$(PROJECT)" --filter "label=COMPONENT=$(COMPONENT)" --format='{{.ID}} {{.Repository}}:{{.Tag}}' | sort -k 2 -u)

-include $(DEPS)
