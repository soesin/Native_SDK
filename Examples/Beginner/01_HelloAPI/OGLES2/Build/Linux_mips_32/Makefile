#
# File			Makefile
# Title			Makes the demo
# Author		PowerVR
#
# Copyright		Copyright (C) by Imagination Technologies Limited.
#

.PHONY: clean

SDKDIR  = $(shell cd ../../../../../.. && pwd)
VPATH = ../..

CROSS_COMPILE ?= mips-linux-gnu-

ifdef TOOLCHAIN
PLAT_CC  = $(TOOLCHAIN)/bin/$(CROSS_COMPILE)gcc
PLAT_CPP = $(TOOLCHAIN)/bin/$(CROSS_COMPILE)g++
PLAT_AR  = $(TOOLCHAIN)/bin/$(CROSS_COMPILE)ar
else
PLAT_CC  = $(CROSS_COMPILE)gcc
PLAT_CPP = $(CROSS_COMPILE)g++
PLAT_AR  = $(CROSS_COMPILE)ar
endif

ifeq "$(X11BUILD)" "1"
ifndef X11ROOT
$(error When building an X11 BUILD you must set X11ROOT to point at the location where your X11 headers and libs can be found.)
endif
ifeq "$(EWSBUILD)" "1"
$(error Cannot have both X11BUILD and EWSBUILD enabled at the same time)
endif
WS_LIBS = -L$(X11ROOT)/lib -lX11 -lXau
WS_INC  = $(X11ROOT)/include
WS = X11
else
ifeq "$(EWSBUILD)" "1"
PLAT_CFLAGS += -DEWS
WS_LIBS = -lews
WS_INC =
WS=EWS
else
WS_LIBS =
WS_INC  =
WS = NullWS
endif
endif

LIBDIR ?= $(SDKDIR)/Builds/Linux/mips_32/Lib
LIBDIR_FLAGS = -L$(LIBDIR) -Wl,--rpath-link,$(LIBDIR):$(WS_RPATH)

ifdef Debug
DEBUG_RELEASE = Debug
PLAT_CFLAGS   += -g
else
DEBUG_RELEASE = Release
PLAT_CFLAGS   += -O2
endif

PLAT_CFLAGS += -Wall -march=mips32r2 -mabi=32 -EL
PLAT_INC  = $(SDKDIR)/Builds/Include $(WS_INC)

PLAT_OBJPATH = $(DEBUG_RELEASE)$(WS)
PLAT_LINK = $(LIBDIR_FLAGS) -lEGL -lGLESv2 -march=mips32r2 -mabi=32 -EL $(WS_LIBS) -L$(TOOLCHAIN)/lib

ifeq "$(WS)" "X11"
SRCNAME = OGLES2HelloAPI_LinuxX11
else
ifeq "$(WS)" "EWS"
SRCNAME = OGLES2HelloAPI_EWS
else
SRCNAME = OGLES2HelloAPI_NullWS
endif
endif

OUTNAME = OGLES2HelloAPI

OBJECTS = $(PLAT_OBJPATH)/$(SRCNAME).o

COMMON_INCLUDES = $(addprefix -I, $(PLAT_INC))

$(PLAT_OBJPATH)/$(OUTNAME) : $(OBJECTS) 
	mkdir -p $(PLAT_OBJPATH)
	$(PLAT_CPP) -o $(PLAT_OBJPATH)/$(OUTNAME) $(OBJECTS) $(LINK) $(PLAT_LINK)

$(PLAT_OBJPATH)/%.o: %.cpp
	mkdir -p $(PLAT_OBJPATH)
	$(PLAT_CC) $(PLAT_CFLAGS) -c $(COMMON_INCLUDES) $(INCLUDES) $^ -o $@

clean:
	-rm -rf $(PLAT_OBJPATH) *.o

