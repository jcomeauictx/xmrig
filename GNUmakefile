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
run: $(TARGETS) $(CONFIG)
	@if [ "$(SUDO)" ]; then \
	 echo If you would rather not use sudo, '`make SUDO=`' >&2; \
	 echo 'But mining will be suboptimal' >&2; \
	fi
	$(SUDO) $< --config $(CONFIG)
$(CONFIG): src/config.json
	sed -e \
	 's/\<YOUR_WALLET_ADDRESS\>/$(MONERO_ADDRESS)/' \
	 $< > $@
src/xmrig: src/Makefile
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
