SHELL := /bin/bash
TARGETS := src/xmrig ../xmrig-cuda/src/libxmrig-cuda.so
CONFIG := $(HOME)/xmrig.json
ORIGIN := $(shell git remote get-url origin)
REQUIRED := cmake libhwloc-dev libuv1-dev libssl-dev nvidia-cuda-dev \
	    nvidia-cuda-toolkit-gcc vim
CUDAFLAGS := -DCMAKE_C_COMPILER=cuda-gcc \
	     -DCMAKE_CXX_COMPILER=cuda-g++ \
	     -DCUDA_HOST_COMPILER=cuda-gcc
SUDO ?= sudo
MONERO_ADDRESS := $(shell cat $(HOME)/moneroaddress.txt)
ifeq ($(MONERO_ADDRESS),)
$(warning You don't have a $(HOME)/moneroaddress.txt file)
$(warning Developer jcomeauictx's chosen address will be used)
$(warning Control-C and fix this if you want mining rewards!)
MONERO_ADDRESS := $(shell cat moneroaddress.txt)
endif
CUDA_LOADER := $(PWD)-cuda/src/libxmrig-cuda.so
ifneq ($(SHOWENV),)
 export
endif
run: $(TARGETS) $(CONFIG)
	@if [ "$(SUDO)" ]; then \
	 echo If you would rather not use sudo, '`make SUDO=`' >&2; \
	 echo 'But mining will be suboptimal' >&2; \
	fi
	$(SUDO) $< --config $(CONFIG)
	@echo NOTE: config will be updated by xmrig on first run
remake:
	$(MAKE) clean run
$(CONFIG): src/config.json
	@echo WARNING: overwriting $(CONFIG) >&2
	sed \
	 -e 's/\<YOUR_WALLET_ADDRESS\>/$(MONERO_ADDRESS)/' \
	 -e 's%\<CUDA_LOADER\>%$(CUDA_LOADER)%' \
	 $< > $@
src/xmrig: src/Makefile src/config.json
	$(MAKE) -C $(@D)
src/Makefile:
	which cmake || sudo $(MAKE) requirements
	cd $(@D) && cmake ..
../xmrig-cuda/src/libxmrig-cuda.so: ../xmrig-cuda/src/Makefile
	$(MAKE) -C $(@D)
../xmrig-cuda/src/Makefile: | ../xmrig-cuda/src
	cd $(@D) && cmake .. $(CUDAFLAGS)
../xmrig-cuda/src:
	cd .. && git clone $(ORIGIN)-cuda
requirements:
	sudo apt install $(REQUIRED)
clean:
	$(MAKE) -C src clean
	$(MAKE) -C ../xmrig-cuda/src clean
	cp -f config.json.orig src/
env:
ifeq ($(SHOWENV),)
	$(MAKE) SHOWENV=1 $@
else
	$@
endif
# systemd user service files
SERVICE_FILES := monerod.service p2pool.service xmrig.service
SYSTEMD_USER_DIR := $(HOME)/.config/systemd/user

install-services: $(SERVICE_FILES)
	@mkdir -p $(SYSTEMD_USER_DIR)
	cp $(SERVICE_FILES) $(SYSTEMD_USER_DIR)/
	systemctl --user daemon-reload
	@echo "Installed service files to $(SYSTEMD_USER_DIR)"
	@echo "Configure wallet in ~/.config/p2pool/env"
	@echo "Then: systemctl --user enable --now monerod p2pool xmrig"
	@if loginctl show-user $(USER) --property=Linger 2>/dev/null \
	    | grep -q 'Linger=no'; then \
	  echo "WARNING: Linger is not enabled for user $(USER)."; \
	  echo "  Services will NOT start at boot without it."; \
	  echo "  Enable with: sudo loginctl enable-linger $(USER)"; \
	elif ! loginctl show-user $(USER) --property=Linger 2>/dev/null \
	    | grep -q 'Linger=yes'; then \
	  echo "WARNING: Could not determine Linger status for user $(USER)."; \
	  echo "  Ensure Linger is enabled: sudo loginctl enable-linger $(USER)"; \
	fi

.PHONY: install-services
.FORCE:
