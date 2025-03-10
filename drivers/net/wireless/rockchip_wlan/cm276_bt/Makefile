# File: Makefile
# Copyright (C) 2007-2018, Marvell International Ltd.
#


#KERNELDIR ?= /home/aka/work/code/rk3399/debian/rk3399_risc/kernel
#CROSS_COMPILE ?= /home/aka/work/code/rk3399/debian/rk3399_risc/prebuilts/gcc/linux-x86/aarch64/gcc-linaro-6.3.1-2017.05-x86_64_aarch64-linux-gnu/bin/aarch64-linux-gnu-


CC=		$(CROSS_COMPILE)gcc
LD=		$(CROSS_COMPILE)ld

BACKUP=		/root/backup
YMD=		`date +%Y%m%d%H%M`

#############################################################################
# Configuration Options
#############################################################################

# Debug Option
# DEBUG LEVEL n/1/2:
# n: NO DEBUG
# 1: PRINTM(MSG,...), PRINTM(FATAL,...), PRINTM(WARN,...) and PRINTM(INFO,...)
# 2: All PRINTM()
CONFIG_DEBUG=1


# SDIO suspend/resume
CONFIG_SDIO_SUSPEND_RESUME=y




#############################################################################
# Select Platform Tools
#############################################################################

MODEXT = ko

ifeq ($(CONFIG_64BIT), y)
	EXTRA_CFLAGS += -DMBT_64BIT
endif

ifeq ($(CONFIG_T50), y)
        EXTRA_CFLAGS += -DT50
        EXTRA_CFLAGS += -DT40
        EXTRA_CFLAGS += -DT3T
endif

ifeq ($(CONFIG_BLE_WAKEUP), y)
        EXTRA_CFLAGS += -DBLE_WAKEUP
endif








KERNELVERSION_X86 := 	$(shell uname -r)
# KERNELDIR?=/lib/modules/$(KERNELVERSION_X86)/build

EXTRA_CFLAGS += -I$(KERNELDIR)/include

EXTRA_CFLAGS += -I$(M)/bt
LD += -S

#ifdef SD8xxx
BINDIR = ../bin_sd8xxx_bt
#endif
BINDIR = ../bin_sd8997_bt


#############################################################################
# Compiler Flags
#############################################################################
	EXTRA_CFLAGS += -DFPNUM='"26"'

ifeq ($(CONFIG_DEBUG),1)
	EXTRA_CFLAGS += -DDEBUG_LEVEL1
endif

ifeq ($(CONFIG_DEBUG),2)
	EXTRA_CFLAGS += -DDEBUG_LEVEL1
	EXTRA_CFLAGS += -DDEBUG_LEVEL2
	DBG=	-dbg
endif

ifeq ($(CONFIG_SDIO_SUSPEND_RESUME),y)
	EXTRA_CFLAGS += -DSDIO_SUSPEND_RESUME
endif

#############################################################################
# Make Targets
#############################################################################

ifneq ($(KERNELRELEASE),)

BTOBJS = bt/bt_main.o bt/bt_sdiommc.o bt/bt_proc.o bt/mbt_char.o


BTOBJS += bt/bt_init.o

ifeq ($(CONFIG_CONCURRENT_DRIVER), y)
obj-m := bt8xxx_sdio.o
bt8xxx_sdio-objs := $(BTOBJS)
else
obj-m := bt8xxx.o
bt8xxx-objs := $(BTOBJS)
endif


# Otherwise we were called directly from the command line; invoke the kernel build system.
else
default:
	$(MAKE) -C $(KERNELDIR) M=$(PWD) modules
endif

###############################################################

export		CC LD EXTRA_CFLAGS KERNELDIR

.PHONY: app/fm_app clean distclean

app/fm_app:
	$(MAKE) -C  $@

echo:

build:		echo default

	@if [ ! -d $(BINDIR) ]; then \
		mkdir $(BINDIR); \
	fi

ifeq ($(CONFIG_CONCURRENT_DRIVER), y)
	cp -f bt8xxx_sdio.$(MODEXT) $(BINDIR)/bt8997_sdio$(DBG).$(MODEXT)
else
	cp -f bt8xxx.$(MODEXT) $(BINDIR)/bt8997$(DBG).$(MODEXT)
endif
	cp -r config $(BINDIR)




	cp -f script/sdio_mmc/* $(BINDIR)/

	cp -f README $(BINDIR)

	$(MAKE) -C app/fm_app $@ INSTALLDIR=$(BINDIR);
	cp -f app/fm_app/fmapp $(BINDIR);

clean:
	-find . -name "*.o" -exec rm {} \;
	-find . -name "*.ko" -exec rm {} \;
	-find . -name ".*.cmd" -exec rm {} \;
	-find . -name "*.mod.c" -exec rm {} \;
	-find . -name "*.symvers" -exec rm {} \;
	-find . -name "modules.order" -exec rm {} \;
	-find . -name ".*.dwo" -exec rm {} \;
	-find . -name "*dwo" -exec rm {} \;
	-rm -rf .tmp_versions
	$(MAKE) -C app/fm_app $@

install: default

distclean:
	-find . -name "*.o" -exec rm {} \;
	-find . -name "*.orig" -exec rm {} \;
	-find . -name "*.swp" -exec rm {} \;
	-find . -name "*.*~" -exec rm {} \;
	-find . -name "*~" -exec rm {} \;
	-find . -name "*.d" -exec rm {} \;
	-find . -name "*.a" -exec rm {} \;
	-find . -name "tags" -exec rm {} \;
	-find . -name ".*" -exec rm -rf 2> /dev/null \;
	-find . -name "*.ko" -exec rm {} \;
	-find . -name ".*.cmd" -exec rm {} \;
	-find . -name "*.mod.c" -exec rm {} \;
	-find . -name ".*.dwo" -exec rm {} \;
	-find . -name "*dwo" -exec rm {} \;
	-rm -rf .tmp_versions
	$(MAKE) -C app/fm_app $@
# End of file;
