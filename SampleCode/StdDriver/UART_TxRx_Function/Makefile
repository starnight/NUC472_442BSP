# Name of the binaries.
PROJ_NAME=UART

######################################################################
#                         SETUP SOURCES                              #
######################################################################

# This is the root directory containing the firmware package,
# the BSP git repository downloaded from here:
# https://github.com/OpenNuvoton/NUC472_442BSP
ROOT_DIR       = ../../../../NUC472_442BSP

# CMSIS library folder
CMSIS_DIR      = $(ROOT_DIR)/Library/CMSIS
# Standard driver library folder
STDDRV_DIR     = $(ROOT_DIR)/Library/StdDriver
# Chip's library folder
CHIPLIB_DIR    = $(ROOT_DIR)/Library/Device/Nuvoton/NUC472_442

# This is where the source files are located, which are not in the
# current directory (the sources of the standard peripheral and device
# libraries, which we use)
SRC_DIRS       = $(STDDRV_DIR)/src
SRC_DIRS      += $(CHIPLIB_DIR)/Source
SRC_DIRS      += $(CHIPLIB_DIR)/Source/GCC

# Tell make to look in that folder if it cannot find a source in the
# current directory
vpath %.c $(SRC_DIRS)

# My source file
SRCS           = main.c

# These source files implement the functions we use.
# make finds them by searching the vpath defined above.
SRCS          += clk.c
SRCS          += uart.c
SRCS          += retarget.c
SRCS          += sys.c

# The system clock initialization of the chip.
SRCS          += system_NUC472_442.c

# Startup file written by Nuvoton
# The assembly code in this file is the first one to be
# executed. Normally you do not change this file.
SRCS          += $(CHIPLIB_DIR)/Source/GCC/startup_NUC472_442.S

# The header files we use are located here
INC_DIRS       = $(CMSIS_DIR)/Include
INC_DIRS      += $(STDDRV_DIR)/inc
INC_DIRS      += $(CHIPLIB_DIR)/Include
INC_DIRS      += .

######################################################################
#                         SETUP TOOLS                                #
######################################################################

# The tool we use
CC         = arm-none-eabi-gcc
OBJCOPY    = arm-none-eabi-objcopy
GDB        = arm-none-eabi-gdb

## Preprocessor options

# directories to be searched for header files
INCLUDE    = $(addprefix -I,$(INC_DIRS))

# #defines needed when working with the NUC library
DEFS       =

## Compiler options
CFLAGS     = -ggdb
# please do not optimize anything because we are debugging
CFLAGS    += -O0
CFLAGS    += -Wall -Wextra -Warray-bounds
CFLAGS    += -mlittle-endian -mthumb -mcpu=cortex-m4 -mthumb-interwork
CFLAGS    += -mfloat-abi=hard -mfpu=fpv4-sp-d16

## Linker options
# tell ld which linker file to use
LFLAGS     = -T$(CHIPLIB_DIR)/Source/GCC/gcc_arm.ld

## Debug/Flash interface and target chip configs
INTERFACE  = "interface/nulink.cfg"
TARGETCHIP = "target/numicro_m4.cfg"

######################################################################
#                         SETUP TARGETS                              #
######################################################################

.PHONY: $(PROJ_NAME)
$(PROJ_NAME): $(PROJ_NAME).elf

$(PROJ_NAME).elf: $(SRCS)
	$(CC) $(INCLUDE) $(DEFS) $(CFLAGS) $(LFLAGS) $^ -o $@
	$(OBJCOPY) -O ihex $(PROJ_NAME).elf   $(PROJ_NAME).hex
	$(OBJCOPY) -O binary $(PROJ_NAME).elf $(PROJ_NAME).bin

flash:
	openocd -f $(INTERFACE) -f $(TARGETCHIP) \
		-c "program $(PROJ_NAME).elf verify reset exit"

clean:
	rm -f *.o $(PROJ_NAME).elf $(PROJ_NAME).hex $(PROJ_NAME).bin

.PHONY: debug
debug:
# before you start gdb, you must start openOCD
	$(GDB) $(PROJ_NAME).elf
