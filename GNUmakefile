SHELL := /bin/bash
TARGETS := src/xmrig ../xmrig-cuda/src/libxmrig-cuda.so
CONFIG := $(HOME)/xmrig.json
ORIGIN := $(shell git remote get-url origin)
run: $(TARGETS)
	$< --config $(CONFIG)
src/xmrig: src/Makefile
	$(MAKE) -C $(@D)
src/Makefile:
	cd $(@D) && cmake ..
../xmrig-cuda/src/libxmrig-cuda.so: ../xmrig-cuda/src/Makefile
	$(MAKE) -C $(@D)
../xmrig-cuda/src/Makefile: | ../xmrig-cuda/src
	cd $(@D) && cmake ..
../xmrig-cuda/src:
	cd .. && git clone $(ORIGIN)-cuda
