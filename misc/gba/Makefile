ifeq ($(strip $(DEVKITPRO)),)
$(error "Please set DEVKITPRO in your environment. export DEVKITPRO=<path to>devkitPro)
endif

include $(DEVKITPRO)/devkitARM/gba_rules


#---------------------------------------------------------------------------------
# Configurable options
#---------------------------------------------------------------------------------
# Name of the final output
TARGET 		= ClassiCube-gba
# List of directories containing source code
SOURCE_DIRS	= src src/gba
# Directory where object files are placed
BUILD_DIR	= build/gba

GAME_TITLE	= ClassiCube
GAME_CODE	= 0000
MAKER_CODE	= 00


#---------------------------------------------------------------------------------
# Code generation
#---------------------------------------------------------------------------------
S_FILES = $(foreach dir,$(SOURCE_DIRS),$(wildcard $(dir)/*.S))
C_FILES = $(foreach dir,$(SOURCE_DIRS),$(wildcard $(dir)/*.c))
OBJS 	= $(addprefix $(BUILD_DIR)/, $(notdir $(C_FILES:%.c=%.o) $(S_FILES:%.S=%.o)))

ARCH	= -mthumb -mthumb-interwork
CFLAGS	= -g -Wall -O2 -DPLAT_GBA -ffunction-sections -fdata-sections -mcpu=arm7tdmi -mtune=arm7tdmi $(ARCH)
ASFLAGS	= -g $(ARCH)

LDFLAGS	= -specs=gba.specs -g $(ARCH)
LIBS	= -ltonc

LIBGBA		:= $(DEVKITPRO)/libgba
INCLUDES 	+= $(foreach dir, $(LIBGBA), -I$(dir)/include)
LDFLAGS 	+= $(foreach dir, $(LIBGBA), -L$(dir)/lib)

LIBTONC		= $(DEVKITPRO)/libtonc
INCLUDES 	+= $(foreach dir, $(LIBTONC), -I$(dir)/include)
LDFLAGS 	+= $(foreach dir, $(LIBTONC), -L$(dir)/lib)

# Dependency tracking
DEPFLAGS = -MT $@ -MMD -MP -MF $(BUILD_DIR)/$*.d
DEPFILES := $(OBJS:%.o=%.d)


#---------------------------------------------------------------------------------
# Main targets
#---------------------------------------------------------------------------------
default: $(BUILD_DIR) $(TARGET).gba

clean:
	rm $(TARGET).gba $(TARGET).elf $(OBJS)

$(BUILD_DIR):
	mkdir -p $(BUILD_DIR)


#---------------------------------------------------------------------------------
# Executable generation
#---------------------------------------------------------------------------------
$(TARGET).elf: $(OBJS)
	$(CC) $(LDFLAGS) $^ -o $@ $(LIBS)

$(TARGET).gba: $(TARGET).elf
	$(OBJCOPY) -O binary $< $@
	gbafix $@ -t$(GAME_TITLE) -c$(GAME_CODE) -m$(MAKER_CODE)


#---------------------------------------------------------------------------------
# Object generation
#---------------------------------------------------------------------------------
$(BUILD_DIR)/%.o: src/%.c
	$(CC) $(CFLAGS) $(INCLUDES) $(DEPFLAGS) -c $< -o $@

$(BUILD_DIR)/%.o: src/gba/%.c
	$(CC) $(CFLAGS) $(INCLUDES) $(DEPFLAGS) -c $< -o $@


#---------------------------------------------------------------------------------
# Dependency tracking
#---------------------------------------------------------------------------------
$(DEPFILES):

include $(wildcard $(DEPFILES))
