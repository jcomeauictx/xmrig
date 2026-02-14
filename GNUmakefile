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
CUDA_LOADER := $(PWD)-cuda/src/libxmrig-cuda.so
endif
run: $(TARGETS) $(CONFIG)
	@if [ "$(SUDO)" ]; then \
	 echo If you would rather not use sudo, '`make SUDO=`' >&2; \
	 echo 'But mining will be suboptimal' >&2; \
	fi
	$(SUDO) $< --config $(CONFIG)
$(CONFIG): src/config.json
	@echo WARNING: overwriting $(CONFIG) >&2
	sed \
	 -e 's/\<YOUR_WALLET_ADDRESS\>/$(MONERO_ADDRESS)/' \
	 -e 's%\<CUDA_LOADER\>%$(CUDA_LOADER)%' \
	 $< > $@
src/xmrig: src/Makefile src/config.json
	$(MAKE) -C $(@D)
	# remaking user config with possibly-modified config.json
	$(MAKE) $(CONFIG)
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
.FORCE:
