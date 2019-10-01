# MicroPython-ST7735-ESP8266

This is a ESP8266 micropython display libraray for ST7735.
It's a fork from  [GuyCarver's ST7735.py](https://github.com/GuyCarver/MicroPython/blob/master/lib/ST7735.py).

This fork added support for newer 0.9" 160x80 mini ST7735 SPI TFT with IPS wide angle view.
Also support 128x128 1.44" and 128x160 1.8" ST7735 TFT. and ensured the startcol, startrows are properly taken cared of even if the screen is rotated to 90 180 270 degrees.

Peformance of this library is slower that the frozen libraries included in firmware directly as the codes are interpreted.

As the ESP8266 only has < 3K of dynamic memory to hold variables and programs being run, you may encounter memory capacity issues if you import the source code st7735.py directly.
You need to use the mpy-cross precompiler to convert the source code st7735.py to the byte-code st7735.mpy
Then copy the st7735.mpy file to the ESP8266's flash memory through a program that can interact with micropython repl. e.g. rshell or mpfshell or ampy.

To use the library, 
You just need these lines of codes to initialze the spi and tft.
Note as ESP8266 has only limited no. of pins, you may want to omit CS  pin to save 1 pin for your other functions. To do that  tie CS pin of your Tft to ground, then omit the cs paramater in the function call)

from ST7735 import TFT
from sysfont import sysfont
from machine import SPI,Pin
import time
import math
#hardware SPI, HSPI
spi = SPI(1, baudrate=8000000, polarity=0, phase=0)
# dc=2, rst=16, cs=0
# full function call to include cs pin
# tft=TFT(spi,2,16,0)

# omit cs to save 1 pin for ESP8266, tie CS of the display to ground.
tft=TFT(spi,2,16)
tft.init_7735(tft.GREENTAB80x160)
# clear the screen
tft.fill(TFT.BLACK)
# draw a square 80 pixels wide
tft.rect((0,0),(80,80, TFT.WHITE)
# display text
tft.text((20, 20), "TEST", TFT.RED, sysfont, 2)

Instead of calling a seperate function to initialize the different types of ST7735 TFT, a paramter is passed to the init function to initize the TFT according to the different model of ST7735, circuit board color or types (tabcolor).
tabcolor
=========
  GREENTAB        = 0x0 # 128x160 , start col 2, start row 1, rgb
  REDTAB          = 0x1 # 128x160 , start col 0, start row 0, rgb
  BLACKTAB        = 0x2 # 128x160 , start col 0, start row 0, bgr
  GREENTAB2       = 0x3 # 128x160 , start col 2, start row 1, bgr
  GREENTAB3       = 0x4 # 128x160 , start col 2, start row 3, rgb
  GREENTAB128x128 = 0x5 # 128x128 1.44 inches, bgr, start col 2
                              #         if rotation = 0, or 1, start row 1
                              #         if rotation = 2, or 3, start row 3
  GREENTAB80x160  = 0x6 # 80x160 0.96 inch, start col 26, start row 1, bgr, inverted
  REDTAB80x160    = 0x7 # 80x160 0.96 inch, start col 24, start row 0, rgb
  BLUETAB         = 0xB # 128x160 , start col 2, start row 1, rgb

If graphicstest.py doesn't work correctly, or misaligned,  try replaceing with another tabcolor above. 

This version is for micropython-esp8266.

A font file is necessary for displaying text (some font files are in [GuyCarver's repo](https://github.com/GuyCarver/MicroPython/tree/master/lib)).

Text nowrap option added(default: nowrap=False).

Pin connections from ST7735 LCD to the ESP8266 

LCD |ESP32-DevKitC
----|----
BLK| N.C. or through a resistor to 3V3 to adjust brightness o ruse PWM to control brightness
CS  |IO0 (or connect to GND to save 1 pin)
DC  |IO16(DC)
RES |IO16
SDA |IO13(MOSI)
SCK |IO14(CLK)
VCC |3V3
GND |GND

[![YouTube image here](https://img.youtube.com/vi/xIy8DPBZsIk/0.jpg)](https://www.youtube.com/watch?v=xIy8DPBZsIk)

tftbmp.py is another sample similar to [Adafruit's tftbmp sketch for Arduino](https://github.com/adafruit/Adafruit-ST7735-Library/blob/master/examples/spitftbitmap/spitftbitmap.ino).

Place bmp file named 'girl128x160.bmp' and 'flower128x160.bmp' in the file system of MicroPython using file uploading tool such as [ampy](https://github.com/adafruit/ampy), rshell, mpfshell, etc.
