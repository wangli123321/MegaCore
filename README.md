# MegaCore
[![Build Status](https://travis-ci.com/MCUdude/MegaCore.svg?branch=master)](https://travis-ci.com/MCUdude/MegaCore) [![MegaCore forum thread](https://img.shields.io/badge/support-forum-blue.svg)](https://forum.arduino.cc/index.php?topic=386733.0)

An Arduino core for most 64 and 100 pin AVRs, all running [Optiboot flash](#write-to-own-flash). This core requires at least Arduino IDE v1.6, where v1.8.5+ is recommended. <br/>
If you're into "native" AVR programming, I'm happy to tell you that all relevant keywords are being highlighted by the IDE through a separate keywords file. Make sure to check out the [example files](https://github.com/MCUdude/MegaCore/tree/master/avr/libraries/AVR_examples/examples) (File > Examples > AVR C code examples).


# Table of contents
* [Supported microcontrollers](#supported-microcontrollers)
* [Supported clock frequencies](#supported-clock-frequencies)
* [Bootloader option](#bootloader-option)
* [BOD option](#bod-option)
* [EEPROM retain option](#eeprom-option)
* [Link time optimization / LTO](#link-time-optimization--lto)
* [Printf support](#printf-support)
* [Pin macros](#pin-macros)
* [PROGMEM with flash sizes greater than 64kB](#progmem-with-flash-sizes-greater-than-64kb)
* [Write to own flash](#write-to-own-flash)
* [Programmers](#programmers)
* **[How to install](#how-to-install)**
  - [Boards Manager Installation](#boards-manager-installation)
  - [Manual Installation](#manual-installation)
  - [PlatformIO](#platformio)
* **[Getting started with MegaCore](#getting-started-with-megacore)**
* [Wiring reference](#wiring-reference)
* **[Pinout](#pinout)**
* **[Minimal setup](#minimal-setup)**


## Supported microcontrollers:

* ATmega6490
* ATmega6450
* ATmega3290
* ATmega3250
* ATmega2561
* ATmega2560
* ATmega1281
* ATmega1280
* ATmega649
* ATmega645
* ATmega640
* ATmega329
* ATmega325
* ATmega169
* ATmega165
* ATmega128
* ATmega64
* AT90CAN128
* AT90CAN64
* AT90CAN32

(All variants - A, L, P, PA, PV, V)
<br/> <br/>
Can't decide what microcontroller to choose? Have a look at the specification table below:

|                  | Mega2560 | Mega1280 | Mega640 | Mega2561 | Mega1281 | Mega128<br/>CAN128 | Mega6490<br/>Mega6450 | Mega64<br/>CAN64<br>Mega649<br/>Mega645 | Mega6490<br/>Mega6450 | CAN32<br/>Mega329<br/>Mega325 | Mega169<br/>Mega165 |
|------------------|----------|----------|---------|----------|----------|--------------------|-----------------------|-----------------------------------------|-----------------------|-------------------------------|---------------------|
| **Flash**        | 256kB    | 128kB    | 64kB    | 256kB    | 128kB    | 128kB              | 64kB                  | 64kB                                    | 32kB                  | 32kB                          | 16kB                |
| **RAM**          | 8kB      | 8kB      | 8kB     | 8kB      | 8kB      | 4kB                | 4kB                   | 4kB                                     | 2kB                   | 2kB                           | 1 kB                |
| **EEPROM**       | 4kB      | 4kB      | 4kB     | 4kB      | 4kB      | 4kB                | 2kB                   | 2kB                                     | 1kB                   | 1kB                           | 512B                |
| **IO pins**      | 70/86 *  | 70/86 *  | 70/86 * | 54       | 54       | 53                 | 68                    | 53                                      | 68                    | 53                            | 53                  |
| **Serial ports** | 4        | 4        | 4       | 2        | 2        | 2                  | 1                     | 2 / 1 **                                | 1                     | 2 / 1 **                      | 1                   |
| **PWM pins**     | 15       | 15       | 15      | 8        | 8        | 7                  | 4                     | 7 / 4 **                                | 4                     | 7 / 4 **                      | 4                   |
| **LED pin**      | PB7      | PB7      | PB7     | PB5      | PB5      | PB5                | PB7                   | PB5                                     | PB7                   | PB5                           | PB5                 |

<b>*</b> Pin 70-85 is not broken out on the Arduino Mega. Make sure to check out the [*AVR style pinout*](#atmega64012802560) for a cleaner an more logical pinout.

<b>**</b> ATmega165/169/325/329/645/649 has one hardware serial port and four PWM outputs.

## Supported clock frequencies

MegaCore supports a variety of different clock frequencies. Select the microcontroller in the boards menu, then select the clock frequency. You'll have to hit "Burn bootloader" in order to set the correct fuses and upload the correct bootloader.
Make sure you connect an ISP programmer, and select the correct one in the "Programmers" menu. For time critical operations an external crystal/oscillator is recommended.

You might experience upload issues when using the internal oscillator. It's factory calibrated but may be a little "off" depending on the calibration, ambient temperature and operating voltage. If uploading failes while using the 8 MHz internal oscillator you have these options:
* Edit the baudrate line in the [boards.txt](https://github.com/MCUdude/MegaCore/blob/6098efe55de668ebb19f538a0b86a2c43e3dec07/avr/boards.txt#L802) file, and choose either 115200, 57600, 38400 or 19200 baud.
* Upload the code using a programmer (USBasp, USBtinyISP etc.) or skip the bootloader by holding down the shift key while clicking the "Upload" button
* Use the 4, 2 or 1 MHz option instead

| Frequency   | Oscillator type             | Speed  | Comment                                           |
|-------------|-----------------------------|--------|---------------------------------------------------|
| 16 MHz      | External crystal/oscillator | 115200 | Default clock on most AVR based Arduino boards    |
| 20 MHz      | External crystal/oscillator | 115200 |                                                   |
| 18.4320 MHz | External crystal/oscillator | 115200 | Great clock for UART communication with no error  |
| 14.7456 MHz | External crystal/oscillator | 115200 | Great clock for UART communication with no error  |
| 12 MHz      | External crystal/oscillator | 57600  | Useful when working with USB 1.1 (12 Mbit/s)      |
| 11.0592 MHz | External crystal/oscillator | 115200 | Great clock for UART communication with no error  |
| 8 MHz       | External crystal/oscillator | 57600  | Common clock when working with 3.3V               |
| 7.3728 MHz  | External crystal/oscillator | 115200 | Great clock for UART communication with no error  |
| 4 MHz       | External crystal/oscillator | 9600   |                                                   |
| 3.6864 MHz  | External crystal/oscillator | 115200 | Great clock for UART communication with no error  |
| 2 MHz       | External crystal/oscillator | 9600   |                                                   |
| 1.8432 MHz  | External crystal/oscillator | 115200 | Great clock for UART communication with no error  |
| 1 MHz       | External crystal/oscillator | 9600   |                                                   |
| 8 MHz       | Internal oscillator         | 38400  | Might cause UART upload issues. See comment above |
| 4 MHz       | Internal oscillator         | 9600   | Derived from the 8 MHz internal oscillator        |
| 2 MHz       | Internal oscillator         | 9600   | Derived from the 8 MHz internal oscillator        |
| 1 MHz       | Internal oscillator         | 9600   | Derived from the 8 MHz internal oscillator        |

## Bootloader option
MegaCore lets you select which serial port you want to use for uploading. UART0 is the default port for all targets, but any hardware serial port may be used.
The original Arduino Mega 2560 bootloader is also supported though a dedicated menu option. Connect your stock Mega 2560, select *Bootloader > Yes (Original Mega 2560 bootloader)* and upload your program as usual.
If your application doesn't need or require a bootloader for uploading code you can also choose to disable this by selecting *No bootloader*. This frees 1024 bytes of flash memory.

Note that you have need to connect a programmer and hit **Burn bootloader** if you want to change any of the *Upload port settings*.


## BOD option
Brown out detection, or BOD for short lets the microcontroller sense the input voltage and shut down if the voltage goes below the brown out setting. To change the BOD settings you'll have to connect an ISP programmer and hit "Burn bootloader". Below is a table that shows the available BOD options:
<br/>

| ATmega640/1280/2560 | ATmega1281/2561 | ATmega3290/6490<br/>ATmega3250/6450 | ATmega165/169<br/>ATmega325/329<br/>ATmega645/649 | ATmega64/128  | AT90CAN32/64/128 |
|---------------------|-----------------|-------------------------------------|---------------------------------------------------|---------------|------------------|
| 4.3V                | 4.3V            | 4.3V                                | 4.3V                                              | 4.0V          | 4.1V             |
| 2.7V                | 2.7V            | 2.7V                                | 2.7V                                              | 2.7V          | 4.0V             |
| 1.8V                | 1.8V            | 1.8V                                | 1.8V                                              |               | 3.9V             |
|                     |                 |                                     |                                                   |               | 3.8V             |
|                     |                 |                                     |                                                   |               | 2.7V             |
|                     |                 |                                     |                                                   |               | 2.6V             |
|                     |                 |                                     |                                                   |               | 2.5V             |
| Disabled            | Disabled        | Disabled                            | Disabled                                          | Disabled      | Disabled         |


## EEPROM option
If you want the EEPROM to be erased every time you burn the bootloader or upload using a programmer, you can turn off this option. You'll have to connect an ISP programmer and hit "Burn bootloader" to enable or disable EEPROM retain. Note that when uploading using a bootloader, the EEPROM will always be retained.


## Link time optimization / LTO
After Arduino IDE 1.6.11 where released, there have been support for link time optimization or LTO for short. The LTO optimizes the code at link time, making the code (often) significantly smaller without making it "slower". In Arduino IDE 1.6.11 and newer LTO is enabled by default. I've chosen to disable this by default to make sure the core keep its backwards compatibility. Enabling LTO in IDE 1.6.10 and older will return an error.
I encourage you to try the new LTO option and see how much smaller your code gets! Note that you don't need to hit "Burn Bootloader" in order to enable LTO. Simply enable it in the "Tools" menu, and your code is ready for compilation. If you want to read more about LTO and GCC flags in general, head over to the [GNU GCC website](https://gcc.gnu.org/onlinedocs/gcc/Optimize-Options.html)!


## Printf support
Unlike the official Arduino cores, MegaCore has printf support out of the box. If you're not familiar with printf you should probably [read this first](https://www.tutorialspoint.com/c_standard_library/c_function_printf.htm). It's added to the Print class and will work with all libraries that inherit Print. Printf is a standard C function that lets you format text much easier than using Arduino's built-in print and println. Note that this implementation of printf will NOT print floats or doubles. This is a limitation of the avr-libc printf implementation on AVR microcontrollers, and nothing I can easily fix.

If you're using a serial port, simply use `Serial.printf("Milliseconds since start: %ld\n", millis());`. Other libraries that inherit the Print class (and thus supports printf) are the LiquidCrystal LCD library and the U8G2 graphical LCD library.


## Pin macros
Note that you don't have to use the digital pin numbers to refer to the pins. You can also use some predefined macros that maps "Arduino pins" to the port and port number:

```c++
// Use PIN_PE0 macro to refer to pin PE0 (Arduino pin 0)
digitalWrite(PIN_PE0, HIGH);

// Results in the exact same compiled code
digitalWrite(0, HIGH);

```

## PROGMEM with flash sizes greater than 64kB
The usual `PROGMEM` attribute stores constant data such as string arrays to flash and is great if you want to preserve the precious RAM. However, PROGMEM will only store content in the lower section, from 0 and up to 64kB. If you want to store data in other sections, you can use `PROGMEM1` (64 - 128kB), `PROGMEM2` (128 - 192kB), or `PROGMEM3` (192 - 256kB), depending on the chip you're using.
Accessing this data is not as straight forward as with `PROGMEM`, but it's still doable:

```c
const char far_away[] PROGMEM1 = "Hello from far away!\n"; // (64  - 128kB)
const char far_far_away[] PROGMEM2 = "Hello from far, far away!\n"; // (128 - 192kB)
const char far_far_far_away[] PROGMEM3 = "Hello from far, far, far away!\n"; // (192 - 256kB)

void print_progmem()
{
  uint8_t i;
  char c;

  // Print out far_away
  for(i = 0; i < sizeof(far_away); i++)
  {
    c = pgm_read_byte_far(pgm_get_far_address(far_away) + i);
    Serial.write(c);
  }

  // Print out far_far_away
  for(i = 0; i < sizeof(far_far_away); i++)
  {
    c = pgm_read_byte_far(pgm_get_far_address(far_far_away) + i);
    Serial.write(c);
  }
  // Print out far_far_far_away
  for(i = 0; i < sizeof(far_far_far_away); i++)
  {
    c = pgm_read_byte_far(pgm_get_far_address(far_far_far_away) + i);
    Serial.write(c);
  }
}

```


## Write to own flash
MegaCore uses Optiboot Flash, a bootloader that supports flash writing within the running application, thanks to the work of [@majekw](https://github.com/majekw).
This means that content from e.g. a sensor can be stored in the flash memory directly without the need of external memory. Flash memory is much faster than EEPROM, and can handle at least 10 000 write cycles before wear becomes an issue.
For more information on how it works and how you can use this in you own application, check out the [Serial_read_write](https://github.com/MCUdude/MegaCore/blob/master/avr/libraries/Optiboot_flasher/examples/Serial_read_write/Serial_read_write.ino) for a simple proof-of-concept demo, and
[Flash_put_get](https://github.com/MCUdude/MegaCore/blob/master/avr/libraries/Optiboot_flasher/examples/Flash_put_get/Flash_put_get.ino) + [Flash_iterate](https://github.com/MCUdude/MegaCore/blob/master/avr/libraries/Optiboot_flasher/examples/Flash_iterate/Flash_iterate.ino) for useful examples on how you can store strings, structs and variables to flash and retrieve then afterwards.
The [Read_write_without_buffer](https://github.com/MCUdude/MegaCore/blob/master/avr/libraries/Optiboot_flasher/examples/Read_write_without_buffer/Read_write_without_buffer.ino) example demonstrate how you can read and write to the flash memory on a lower level without using a RAM buffer.


## Programmers
Select your microcontroller in the boards menu, then select the clock frequency. You'll have to hit "Burn bootloader" in order to set the correct fuses and upload the correct bootloader. <br/>
Make sure you connect an ISP programmer, and select the correct one in the "Programmers" menu. For time critical operations an external oscillator is recommended.


## How to install
#### Boards Manager Installation
This installation method requires Arduino IDE version 1.6.4 or greater.
* Open the Arduino IDE.
* Open the **File > Preferences** menu item.
* Enter the following URL in **Additional Boards Manager URLs**: `https://mcudude.github.io/MegaCore/package_MCUdude_MegaCore_index.json`
* Open the **Tools > Board > Boards Manager...** menu item.
* Wait for the platform indexes to finish downloading.
* Scroll down until you see the **MegaCore** entry and click on it.
* Click **Install**.
* After installation is complete close the **Boards Manager** window.

#### Manual Installation
Click on the "Download ZIP" button in the upper right corner. Extract the ZIP file, and move the extracted folder to the location "**~/Documents/Arduino/hardware**". Create the "hardware" folder if it doesn't exist.
Open Arduino IDE, and a new category in the boards menu called "MegaCore" will show up.

#### PlatformIO
[PlatformIO](http://platformio.org) is an open source ecosystem for IoT development and supports MegaCore.

**See [PlatformIO.md](https://github.com/MCUdude/MegaCore/blob/master/PlatformIO.md) for more information.**


## Getting started with MegaCore
Ok, so you're downloaded and installed MegaCore, but how to get started? Here's a quick start guide:
* Hook up your microcontroller as shown in the [pinout diagram](#pinout).
  - If you're not planning to use the bootloader (uploading code using a USB to serial adapter), the FTDI header and the 100 nF capacitor on the reset pin can be omitted.
* Open the **Tools > Board** menu item, select **MegaCore** and select our preferred target.
* 
* Select your preferred clock frequency. **16 MHz** is standard on most Arduino boards.
* Select what kind of programmer you're using under the **Programmers** menu.
* Hit **Burn Bootloader**. If an LED is connected to pin PB5/PB7, it should flash twice every second.
* Now that the correct fuse settings is sat and the bootloader burnt, you can upload your code in two ways:
  - Disconnect your programmer tool, and connect a USB to serial adapter to the microcontroller, like shown in the [pinout diagram](#pinout). Then select the correct serial port under the **Tools** menu, and click the **Upload** button. If you're getting some kind of timeout error, it means your RX and TX pins are swapped, or your auto reset circuity isn't working properly (the 100 nF capacitor on the reset line).
  - Keep your programmer connected, and hold down the `shift` button while clicking **Upload**. This will erase the bootloader and upload your code using the programmer tool.

Your code should now be running on the microcontroller!

## Wiring reference
To extend this core's functionality a bit further, I've added a few missing Wiring functions to this hardware package. As many of you know Arduino is based on Wiring, but that doesn't mean the Wiring development isn't active. These functions is used as "regular" Arduino functions, and there's no need to include an external library.<br/>
I hope you find this useful, because they really are!

### Function list
* portMode()
* portRead()
* portWrite()
* sleepMode()
* sleep()
* noSleep()
* enablePower()
* disablePower()

### For further information please view the [Wiring reference page](https://github.com/MCUdude/MegaCore/blob/master/Wiring_reference.md)!


## Pinout

### 64-pin chips
Since there are no standardized Arduino pinout for this chip family, I've created one. I've tried to make it as simple and logical as possible. This pinout makes great sense if you're buying this [cheap breakout boards](http://www.ebay.com/itm/381547311629) at Ebay or AliExpress (just make sure to remove C3 in order to get auto reset working). The standard LED pin is assigned to Arduino pin 13 (PB5), and will blink twice if you hit the reset button.

| ATmega64/128/1281/2561<br/>AT90CAN32/CAN64/CAN128      | ATmega165/169/325/329/645/649                           |
|--------------------------------------------------------|---------------------------------------------------------|
|<img src="https://i.imgur.com/sweRJs3.jpg" width="280"> | <img src="https://i.imgur.com/YJ3ojm1.png" width="280"> |

### 100-pin chips
Beside including the original Arduino Mega pinout for the ATmega640/1280/2560, I've also added an *AVR style pinout*, which is a more straight forward and logical pinout if you're not working with the Arduino Mega board. For the default Arduino Mega pinout, the standard LED pin is assigned to Arduino pin 13, and for the AVR pin it's assigned to pin 22.
<b>Click to enlarge:</b> <br/>

| ATmega640/1280/2560<br/>Arduino MEGA pinout             | ATmega640/1280/2560<br/>"AVR" pinout                   | ATmega3250/3290/6450/6490                               |
|---------------------------------------------------------|--------------------------------------------------------|---------------------------------------------------------|
| <img src="https://i.imgur.com/O7WtWAj.jpg" width="240"> | <img src="http://i.imgur.com/DfR7arD.jpg" width="240"> | <img src="https://i.imgur.com/F80M5u1.jpg" width="240"> |



## Minimal setup
Here's some simple schematics showing a minimal setup using an external crystal. Omit the crystal and the two 22pF capacitors if you're using the internal oscillator. <br/>
<b>Click to enlarge:</b> <br/>

| ATmega64/128/1281/2561<br/>AT90CAN32/CAN64/CAN128      | ATmega165/325/645<br/>ATmega169/329/649                 | ATmega640/1280/2560                                    | ATmega3250/6450<br/>ATmega3290/6490                    |
|--------------------------------------------------------|---------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
|<img src="https://i.imgur.com/Ca4ASgH.png" width="200"> | <img src="https://i.imgur.com/d5Otnpj.png" width="200"> | <img src="http://i.imgur.com/gQS1ORv.png" width="200"> |<img src="https://i.imgur.com/jdPptok.png" width="200"> |

