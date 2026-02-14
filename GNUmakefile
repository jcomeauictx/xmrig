SHELL := /bin/bash
TARGETS := src/xmrig ../xmrig-cuda/src/libxmrig-cuda.so
CONFIG := $(HOME)/xmrig.json
ORIGIN := $(shell git remote get-url origin)
REQUIRED := cmake libhwloc-dev libuv1-dev libssl-dev nvidia-cuda-dev \
	    nvidia-cuda-toolkit-gcc vim
run: $(TARGETS)
	sudo $< --config $(CONFIG)
src/xmrig: src/Makefile
	$(MAKE) -C $(@D)
src/Makefile:
	which cmake || sudo $(MAKE) requirements
	cd $(@D) && cmake ..
../xmrig-cuda/src/libxmrig-cuda.so: ../xmrig-cuda/src/Makefile
	$(MAKE) -C $(@D)
../xmrig-cuda/src/Makefile: | ../xmrig-cuda/src
	cd $(@D) && cmake ..
../xmrig-cuda/src:
	cd .. && git clone $(ORIGIN)-cuda
requirements:
	sudo apt install $(REQUIRED)
