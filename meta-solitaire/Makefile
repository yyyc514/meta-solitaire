CC=gcc
CPP=g++
INO=meta-solitaire.ino
BINARY=meta-solitaire
ORIG_FILES=$(wildcard *.cpp) $(wildcard *.h)
BUILD_DIR=./build
BUILD_FILES=build/sketch/$(INO).cpp \
	$(foreach bname, $(notdir $(ORIG_FILES)), $(BUILD_DIR)/sketch/$(bname))
BUILD_SRC=$(filter %.cpp %.o, $(BUILD_FILES))

export SKETCH_NAME='"""$(INO)"""'

DEFINES=-DARDUINO=185 -D__arm__ -DSPI_HAS_TRANSACTION -D__SKETCH_NAME__=$(SKETCH_NAME)
CFLAGS=-std=c++11 $(INC) $(DEFINES) -w -g

META_LIB_DIR=../../Gamebuino-Meta/src/

# arduino tools
ARDUINO_HARDWARE=-hardware /Applications/Arduino.app/Contents/Java/hardware \
	-hardware $(HOME)/Library/Arduino15/packages \
	-hardware $(HOME)/Documents/Arduino/hardware
ARDUINO_TOOLS=-tools /Applications/Arduino.app/Contents/Java/tools-builder \
	-tools /Applications/Arduino.app/Contents/Java/hardware/tools/avr \
	-tools $(HOME)/Library/Arduino15/packages
ARDUINO_LIBS=-libraries $(HOME)/Documents/Arduino/libraries
ARDUINO_BUILT_INS=-built-in-libraries /Applications/Arduino.app/Contents/Java/libraries
AB=/Applications/Arduino.app/Contents/Java/arduino-builder
# AB_ARGS=-verbose



INC = -I. `sdl2-config --cflags --libs` -I$(META_LIB_DIR)/stub/ -I$(META_LIB_DIR)

all: dirs meta $(BINARY)

dirs: build

build:
	mkdir -p build

build/sketch/%.cpp: %.cpp
	cp *.cpp ./build/sketch

build/sketch/%.h: %.h
	cp *.h ./build/sketch

build/sketch/$(INO).cpp: $(INO)
	$(AB) -preprocess $(AB_ARGS) $(ARDUINO_HARDWARE) $(ARDUINO_TOOLS) $(ARDUINO_LIBS) \
		$(ARDUINO_BUILT_INS) -fqbn=gamebuino:samd:gamebuino_meta_native -ide-version=10805 \
		-build-path ./build -warnings=none -prefs=build.warn_data_percentage=75 $(INO)

META_LIBS = $(META_LIB_DIR)/build/meta.o $(META_LIB_DIR)/stub/core.cpp

meta:
	$(MAKE) -C $(META_LIB_DIR)

$(BINARY): $(BUILD_FILES) $(META_LIBS)
	$(CPP) $(CFLAGS) -o $(BINARY) $(BUILD_SRC) $(META_LIBS)

cleanmeta:
	rm $(META_LIB_DIR)/build/meta.o $(META_LIB_DIR)/build/Gamebuino-Meta.o
	$(MAKE) -C $(META_LIB_DIR) clean

clean:
	# we always need to rebuild Gamebuino-Meta.o because of __SKETCH_NAME__
	rm $(META_LIB_DIR)/build/meta.o $(META_LIB_DIR)/build/Gamebuino-Meta.o
	rm -rf ./build
	rm $(BINARY)
