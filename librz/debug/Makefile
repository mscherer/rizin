include ../config.mk

NAME=rz_debug
RZ_DEPS=rz_reg rz_analysis rz_bp rz_io rz_parse rz_cons rz_syscall rz_hash rz_flag rz_util rz_egg rz_crypto
RZ_DEPS+=rz_socket
CFLAGS+=-DRZ_PLUGIN_INCORE

ifeq ($(OSTYPE),bsd)
LDFLAGS=-lkvm
endif

foo:
	@${MAKE} libs
	@${MAKE} plugins

include ../config.mk
include ${STATIC_DEBUG_PLUGINS}

# Enable pure-MACH debugging by default.
# The ptrace one will be deprecated at some point
XNU_USE_PTRACE?=0
CFLAGS+=-DXNU_USE_PTRACE=$(XNU_USE_PTRACE)

STATIC_OBJS=$(subst ..,p/..,$(subst debug_,p/debug_,$(STATIC_OBJ)))

OBJS=dsignal.o dmap.o trace.o arg.o debug.o plugin.o snap.o dsession.o
OBJS+=pid.o dreg.o ddesc.o desil.o ${STATIC_OBJS}

ifeq (${OSTYPE},darwin)
ifneq (${NATIVE_OBJS},)
OBJS+= p/${NATIVE_OBJS}
endif
endif

ifeq ($(OSTYPE),$(filter $(OSTYPE),gnulinux android))
CFLAGS+=-I../bin/format/elf
OBJS+=$(addprefix p/,$(NATIVE_OBJS))
endif

ifneq (${COREDUMP_OBJS},)
ifeq ($(OSTYPE),$(filter $(OSTYPE),gnulinux))
OBJS+= p/${COREDUMP_OBJS}
endif
endif

ifneq (,$(findstring bsd,$(OSTYPE))$(findstring dragonfly,$(OSTYPE)))
ifneq (${NATIVE_OBJS},)
OBJS+=$(addprefix p/,$(NATIVE_OBJS))
endif
endif

plugins:
	$(MAKE) -C p all

ios:
	cd ../.. && sys/rebuild.sh iosdbg

include ../rules.mk

libs: ${LIBSO} ${LIBAR}

.PHONY: foo plugins tests libs ios
