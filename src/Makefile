#******************************************************************************
# Copyright (C) 2017 by Alex Fosdick - University of Colorado
#
# Redistribution, modification or use of this software in source or binary
# forms is permitted as long as the files maintain this copyright. Users are 
# permitted to modify this and use it to learn about the field of embedded
# software. Alex Fosdick and the University of Colorado are not liable for any
# misuse of this material. 
#
#*****************************************************************************

#------------------------------------------------------------------------------
# This is a make file that supports Host (Linux) and Cross platform: ARM
#
# Use: make [TARGET] [PLATFORM-OVERRIDES]
#
# Build Targets:
#      <FILE>.o - Builds <FILE>.o object file for source files but doesn't
#	          link.
#
#      <FILE>.i - Generate the preprocessed output of the c implementation
#                 files using -E flag. 
#		  
#      <FILE>.asm - Generate the assembly ouetput of c implementation
#		    files and final output executable. Use -S flag and the
#		    objdump utility.
#
#
#
#      compile - all: Compiles all files, doesn't build
#                build: Compile all object files and link them into a final
#                       executable.
#                clean: removes all generated files. 
#
# Platform Overrides:
#      CPU - ARM Cortex Architecture (cortex-m4)
#      ARCH - ARM Architecture (thumb) so 16 bit wide ISA
#      SPECS - Specs file to give the linker
#
#------------------------------------------------------------------------------
include sources.mk

# Platform Overrides
ifeq ($(PLATFORM),MSP432)
CC = arm-none-eabi-gcc
LD = arm-none-eabi-ld
LINKER_FILE = ../msp432p401r.lds # Need to provide different linker file because diff
                                 # architecture.  Otherwise it will look for sys link
LDFLAGS = -Wl,-Map=$(TARGET).map -T$(LINKER_FILE)
ARMFLAGS = -mcpu=$(CPU) -m$(ARCH) --specs=$(SPECS) -march=armv7e-m -mfloat-abi=hard -mfpu=$(FPU)

# Architectures Specific Flags or Overrides
CPU = cortex-m4
ARCH = thumb
SPECS = nosys.specs
FPU = fpv4-sp-d16

# Objdump utility to produce assembly code
OBJDUMP = arm-none-eabi-objdump

# Size Utility that gives sizes of compiled sections inside object files and executables
SIZE = arm-none-eabi-size

else

# Compiler Flags and Defines
CC = gcc 
LD = ld

# Size Utility
SIZE = size
LDFLAGS = -Wl, -Map=$(TARGET).map
OBJDUMP	 = objdump

endif

# Compiler Flags and Defines

TARGET = c1m2


CFLAGS = -Wall -Werror -g -O0 -std=c99 -D$(PLATFORM) $(ARMFLAGS)
CPPFLAGS = -E
Main = main
OBJS = $(SOURCES:.c=.o) # Variable that is assigned the making of object files from .c files

PREPRO = $(SOURCES:.c=.i) # Variabe that is assigned making of .i implementation files from .c
                          # files

ASM = $(SOURCES:.c=.asm) # Variable that is assigned making of .asm assembly code files from .c
                         #files

DEPS = $(SOURCES:.c=.d) # Variable that is assigned making of dependency files for .c files

FILES = *.asm *.i *.o *.d

%.o : %.c
	$(CC) $(INCLUDES) -c $< $(CFLAGS) -o $@ # make .o files from .c files and run compiler
                                                # with compiler options and flags/overrides
                                                # need includes and automatic variables
%.i : %.c
	$(CC) $(INCLUDES) $(CPPFLAGS) $< $(CFLAGS) -o $@ # rule for making .i files

%.asm: %.c
	$(CC) $(INCLUDES) -S $< $(CFLAGS) -o $@

$(TARGET).asm : $(TARGET).out
	$(OBJDUMP) -d $(TARGET).out >> $@

%.d : %.c
	$(CC) $(INCLUDES) -M $< $(CFLAGS) -o $@

.PHONY: compile-all
compile-all: $(OBJS)
	$(CC) $(INCLUDES) $(OBJS) -c $(CFLAGS) -o $(TARGET).o

.PHONY: build
build: all
.PHONY: all
all: $(TARGET).out  $(TARGET).asm


$(TARGET).out: $(OBJS) $(DEPS)
	$(CC) $(CFLAGS) $(INCLUDES) $(LDFLAGS) -o $@ $(OBJS)
	$(SIZE) -Atd $(TARGET).out
	$(SIZE) $(TARGET).out
.PHONY: clean
clean:
	rm -f $(TARGET).asm $(FILES) $(TARGET).out $(TARGET).map $(PREPRO) $(ASMS) $(DEPS) $(OBJS)
