# Makefile for building the tock kernel for the nRF development kit

TOCK_ARCH=cortex-m0
TARGET=thumbv6m-none-eabi
PLATFORM=nrf51dk

include ../Makefile.common

.PHONY: apps/$(APP)/build/$(TOCK_ARCH)/app.bin
apps/$(APP)/build/$(TOCK_ARCH)/app.bin:
	@make -C apps/$(APP) TOCK_ARCH=$(TOCK_ARCH)

target/$(TARGET)/release/nrf51dk-$(APP): target/$(TARGET)/release/nrf51dk apps/$(APP)/build/$(TOCK_ARCH)/app.bin
	@$(OBJCOPY) --update-section .apps=../../userland/examples/$(APP)/build/$(TOCK_ARCH)/app.bin \
	  --set-section-flags .apps=alloc,code \
	  target/$(TARGET)/release/nrf51dk $@

target/$(TARGET)/release/nrf51dk-$(APP).hex: target/$(TARGET)/release/nrf51dk-$(APP)
	@$(OBJCOPY) -Oihex $^ $@

JLINK=JLinkExe
JLINK_OPTIONS+=-device nrf51422 -if swd -speed 1200 -AutoConnect 1
JLINK_SCRIPTS_DIR=jtag/

# Upload the kernel over JTAG
.PHONY: program
flash: target/$(TARGET)/release/nrf51dk.hex
	$(JLINK) $(JLINK_OPTIONS) $(JLINK_SCRIPTS_DIR)/flash-kernel.jlink

# Upload kernel + app over JTAG
.PHONY: program-full
flash-full: target/$(TARGET)/release/nrf51dk-$(APP).hex
	@$(eval TEMPFILE := $(shell mktemp -t nrf51dk-$(APP).jlink.XXXXXXXXXX))
	@echo r > $(TEMPFILE)
	@echo loadfile $< >> $(TEMPFILE)
	@echo r >> $(TEMPFILE)
	@echo g >> $(TEMPFILE)
	@echo q >> $(TEMPFILE)
	$(JLINK) $(JLINK_OPTIONS) $(TEMPFILE)
	@rm $(TEMPFILE)

