# -*- Makefile -*-

# Binaries
CC    = gcc
MKDIR = mkdir -p
VALGRIND = valgrind --tool=memcheck --leak-check=full --show-leak-kinds=all

# Binary name
BIN  	 = linked_list
BIN_LIB	 = lib$(BIN).so
BIN_TEST = $(BIN)-test
CVG_FILE = coverage_report.html

# Folders and paths
SRC_DIR	  	= src
INC_DIR   	= ./include
INC_PRJ_DIR = ../include
INC_TST_DIR = ./test/include
TST_DIR	  	= test/src
BUILD_DIR 	= build
PRJ_LIB_DIR = ../libs
#LD_LIBRARY_PATH :=`pwd`/$(BUILD_DIR):$(LD_LIBRARY_PATH)

# CC flags and libs
CFLAGS 		  = -fPIC -Wall -Wextra -g -O0 -I$(INC_DIR) -I$(INC_PRJ_DIR)
LDFLAGS 	  = -shared
PROFILE_FLAGS = -fprofile-arcs -ftest-coverage -I$(INC_TST_DIR)
TST_LIBS 	  = -lcheck -lm -lpthread -lrt
COV_LIBS 	  = -lgcov -coverage
LIBS   		  =

# GCONV
GCOV = gcovr
GCONV_FLAGS = -r . --html --html-details

# Source files and object files
SRC_FILES  = $(shell find $(SRC_DIR) -name '*.c')
TST_FILES  = $(shell find $(TST_DIR) -name '*.c')
POBJ_FILES = $(addprefix $(BUILD_DIR)/,$(patsubst %.c,%.o,$(notdir $(SRC_FILES))))
LOBJ_FILES = $(addprefix $(BUILD_DIR)/,$(filter-out main.o, $(patsubst %.c,%.o,$(notdir $(SRC_FILES)))))
TOBJ_FILES = $(addprefix $(BUILD_DIR)/,$(patsubst %.c,%.o,$(notdir $(TST_FILES))))

SILENT:=@

.PHONY: build all test lib coverage valgrind clean

build: directories $(BIN)
	@echo "Done, enjoy your binary files :D"

all: build lib coverage

directories:
	@echo "Creating directories $(BUILD_DIR)"
	$(SILENT) $(MKDIR) $(BUILD_DIR)

$(BUILD_DIR)/%.o: $(SRC_DIR)/%.c
	@echo "Compiling project file '$<' to create '$(BUILD_DIR)/$@'"
	$(SILENT) $(CC) -c -o $@ $< $(CFLAGS)

$(BUILD_DIR)/%.o: $(TST_DIR)/%.c
	@echo "Compiling test file '$<' to create '$(BUILD_DIR)/$@'"
	$(SILENT) $(CC) -c -o $@ $< $(CFLAGS) $(PROFILE_FLAGS)

$(BIN): $(POBJ_FILES)
	@echo "Linking the object files ($^) to build '$(BUILD_DIR)/$@' executable file"
	$(SILENT) $(CC) -o $(BUILD_DIR)/$@ $^ $(CFLAGS) $(LIBS)

$(BIN_LIB): $(LOBJ_FILES)
	@echo "Linking the object files ($^) to build '$(BUILD_DIR)/$@' library file"
	$(SILENT) $(CC) -o $(BUILD_DIR)/$@ $^ $(CFLAGS) $(LDFLAGS) $(LIBS)

$(BIN_TEST): $(LOBJ_FILES) $(TOBJ_FILES)
	@echo "Building the test binary ($(BUILD_DIR)/$@) out of $^"
	$(SILENT) $(CC) -o $(BUILD_DIR)/$@ $^ $(TST_LIBS) $(COV_LIBS)

lib: directories $(BIN_LIB)

lib_install: lib
	@echo "Copying the '$(BUILD_DIR)/$(BIN_LIB)' file to the $(PRJ_LIB_DIR)"
	$(SILENT) cp $(BUILD_DIR)/$(BIN_LIB) $(PRJ_LIB_DIR)


test: directories $(BIN_TEST)
	@echo ""
	@echo "--- TESTS BEGIN ------------------------------------"
	$(BUILD_DIR)/$(BIN_TEST)
	@echo ""
	@echo "---- TESTS END --------------------------------------"
	@echo ""

valgrind:
	RET=0
	@echo "Checking binaries to run valgrind..."
ifneq ("$(wildcard $(BUILD_DIR)/$(BIN))","")
	@echo "Running valgrind against $(BUILD_DIR)/$(BIN)"
	$(SILENT) RET=`$(VALGRIND) --log-file=$(BUILD_DIR)/valgrind-$(BIN).txt $(BUILD_DIR)/$(BIN)`
	@echo "Done with valgrind! You can find the restult at $(BUILD_DIR)/valgrind-$(BIN).txt"
endif

ifneq ("$(wildcard $(BUILD_DIR)/$(BIN_TEST))","")
	@echo "Running valgrind against $(BUILD_DIR)/$(BIN_TEST)"
	$(SILENT) RET=`$(VALGRIND) --log-file=$(BUILD_DIR)/valgrind-$(BIN_TEST).txt $(BUILD_DIR)/$(BIN_TEST)`
	@echo "Done with valgrind! You can find the restult at $(BUILD_DIR)/valgrind-$(BIN_TEST).txt"
endif

coverage: $(CVG_FILE)

$(CVG_FILE): test
	@echo "Building coverage file $(BUILD_DIR)/$(CVG_FILE)"
	$(SILENT) $(GCOV) $(GCONV_FLAGS) -o $(BUILD_DIR)/$(CVG_FILE)

clean:
	$(SILENT) rm -Rf $(BUILD_DIR)



