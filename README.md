ABOUT
=====

I adapted this from the Makefile at
[http://mjo.tc/atelier/2009/02/arduino-cli.html](http://mjo.tc/atelier/2009/02/arduino-cli.html).

Take a look at `Makefile.serial` and `Makefile.avrispmkii` for example
Makefiles.

UPDATE: Upgraded to support Arduino 1.0!

STANDARD ARDUINO WORKFLOW
=========================

Given a normal sketch directory, all you need to do is to create
a small Makefile which defines a few things, and then includes this one.

For example:

    ARDUINO_DIR  = /Applications/arduino-0013

    TARGET       = CLItest
    ARDUINO_LIBS = LiquidCrystal

    MCU          = atmega168
    F_CPU        = 16000000
    ARDUINO_PORT = /dev/cu.usb*

    include /usr/local/share/Arduino.mk

Hopefully these will be self-explanatory but in case they're not:

- `ARDUINO_DIR` : Where the Arduino software has been unpacked
- `TARGET` : The basename used for the final files. Canonically
              this would match the .pde file, but it's not needed
              here: you could always set it to xx if you wanted!
- `ARDUINO_LIBS` : A list of any libraries used by the sketch (we assume
              these are in $(ARDUINO_DIR)/libraries
- `MCU` : The target processor
- `F_CPU` : The target processor frequency
- `ARDUINO_PORT` : The port where the Arduino can be found (only needed
              when uploading

Once this file has been created the typical workflow is just

    $ make upload

All of the object files are created in the build-cli subdirectory
All sources should be in the current directory and can include:
 - at most one .pde file which will be treated as C++ after the standard
   Arduino header and footer have been affixed.
 - any number of .c, .cpp, .s and .h files


Besides make upload you can also

- `make` : no upload
- `make clean` : remove all our dependencies
- `make depends` : update dependencies
- `make reset` : reset the Arduino by tickling DTR on the serial port
- `make raw_upload` : upload without first resetting


ARDUINO WITH OTHER TOOLS
========================

If the tools aren't in the Arduino distribution, then you need to 
specify their location:

    AVR_TOOLS_PATH = /usr/bin
    AVRDUDE_CONF   = /etc/avrdude/avrdude.conf


ARDUINO WITH ISP
================

You need to specify some details of your ISP programmer and might
also need to specify the fuse values:

    ISP_PROG	   = stk500v2
    ISP_PORT     = /dev/ttyACM0
    
    ISP_LOCK_FUSE_PRE  = 0x3f
    ISP_LOCK_FUSE_POST = 0xcf
    ISP_HIGH_FUSE      = 0xdf
    ISP_LOW_FUSE       = 0xff
    ISP_EXT_FUSE       = 0x01

I think the fuses here are fine for uploading to the ATmega168
without bootloader.

To actually do this upload use the ispload target:

    make ispload

