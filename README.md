# grbl32

## Credits

[robomechs/6-AXIS-USBCNC-GRBL](https://github.com/robomechs/6-AXIS-USBCNC-GRBL), based on [usbcnc/grbl](https://github.com/usbcnc/grbl) for the STM32 code.

[gnea/grbl](https://github.com/gnea/grbl) for the core GRBL code.

***
## Highlights

* 3-axis 32-bit GRBL firmware
* USB or USART communication
* USART serial baud rate: 921600
* Up to 6 axis
* Can be used with the "Blue Pill" STM32F103C8 board or any equivalent circuit
* The code can be edited with your favorite text editor and built from the command line on a Mac or Linux computer. Config included for MS Visual Studio Code
* Compiled .hex firmware files are available in the Release directory

***
## Description

This is GRBL 1.1f, a firmware used to control a CNC milling machine or laser engraver/cutter, for the STM32F103 micro-controller. That MCU is used on the very cheap "Blue Pill" board.

A few other 32-bit GRBL repositories can be found on Github but most of these use dedicated IDE's like Eclipse or STM32CubeIDE and are not easy to use on a non-Windows computer.

I wanted to be able to edit the code with a text editor like Atom or MS Visual Studio Code and to build and flash the firmware from the command line.

This repository is mostly an adaptation of the grbl32 repository from [trobomechs/6-AXIS-USBCNC-GRBL](https://github.com/robomechs/6-AXIS-USBCNC-GRBL).

My intention is to use this as part of a larger project which will have a CNC controller board of my own design including the STM32 MCU, the stepper drivers, opto-coupled inputs for the limit switches, PWM spindle speed control and a buck converter providing 3.3V from the 24V stepper motor power supply. I will have the PCB fully assembled from [JLCPCB](https://jlcpcb.com).

Another project to come is to stream the gcode and GRBL commands from an ESP32 board with touch screen, jog controls and WIFI to upload the gcode files from my Mac to the CNC. This, I already do with a Raspberry Pi clone connected to my Arduino Nano/CNC shield V4.

Please note that this is still work in progress and not yet fully tested in real CNC operation. Use at your own risk.

***
## Required hardware 

#### A Blue Pill STM32F103 board

<img src="/doc/img/BluePill.jpg">

Available [here](https://www.amazon.de/dp/B07CRHX5F5/ref=sspa_dk_detail_0?pd_rd_i=B07CRHYTDG&pd_rd_w=BfiZr&pf_rd_p=d3e24f85-c2f2-4959-bef4-3acc5e4e81dc&pd_rd_wg=g8SUT&pf_rd_r=2KXN58GYSKCJW51FNP21&pd_rd_r=665a1ebb-2ed4-4c9e-909b-bb4b300e20a4&spLa=ZW5jcnlwdGVkUXVhbGlmaWVyPUFVWDIyTVpWUkJQTlQmZW5jcnlwdGVkSWQ9QTAzMjQxNjQzRFhMSE9ONUtWMERSJmVuY3J5cHRlZEFkSWQ9QTA1MDE5MjhQR0lLTk5TRU45ODgmd2lkZ2V0TmFtZT1zcF9kZXRhaWwmYWN0aW9uPWNsaWNrUmVkaXJlY3QmZG9Ob3RMb2dDbGljaz10cnVl&th=1).

#### A cheap chinese ST-LINK V2 programmer

<img src="/doc/img/ST-LINK V2.jpg">

Like [this one](https://www.amazon.de/-/en/dp/B07QBLNDPM/ref=sr_1_fkmr1_1?dchild=1&keywords=azdelivery+st-link&qid=1585972853&s=industrial&sr=8-1-fkmr1).

#### A USB to FTDI adapter for testing the USART interface

<img src="/doc/img/FTDI.jpg">

This one seems to work at 921600 baud. You can get it [here](https://www.amazon.de/dp/B01N9RZK6I/ref=sspa_dk_detail_2?psc=1&pd_rd_i=B01N9RZK6I&pd_rd_w=EiSKu&pf_rd_p=d3e24f85-c2f2-4959-bef4-3acc5e4e81dc&pd_rd_wg=LoeX3&pf_rd_r=ZZ9HJ0736BW5XP9AV9Y1&pd_rd_r=310718f1-2cd9-4810-8e25-4d5afcdf4522&spLa=ZW5jcnlwdGVkUXVhbGlmaWVyPUExVElTVFM0UU1ENTlQJmVuY3J5cHRlZElkPUEwNzY0MTU4MkdRRlZPU0tOWkRURCZlbmNyeXB0ZWRBZElkPUEwNzEyNDIxVkZZREQ5TjVDWkpYJndpZGdldE5hbWU9c3BfZGV0YWlsJmFjdGlvbj1jbGlja1JlZGlyZWN0JmRvTm90TG9nQ2xpY2s9dHJ1ZQ==).

***
## Required software

#### The GNU Embedded Toolchain for Arm

##### Mac
Note: homebrew must be installed.

```
brew install gcc-arm-none-eabi
```

##### Linux

Install the toolchain as per your Linux distro instructions. For example on debian/ubuntu:
```
sudo apt-get update
sudo apt-get install gcc-arm-none-eabi
```

#### The ST-Link software

##### Mac

```
brew install stlink
```

##### Linux

##### Ubuntu 19.04 or newer:
```
sudo apt-get install stlink-tools
```
##### Older debian/ubuntu distros:

```
sudo apt-get install libusb-1.0-0-dev git cmake build-essential
git clone https://github.com/texane/stlink stlink.git
cd stlink.git
make
cd build/Release
sudo make install
```
Test with:
```
st-info
```
If you get the message `st-info: error while loading shared libraries: libstlink.so.1: cannot open shared object file: No such file or directory`:
```
echo "export LD_LIBRARY_PATH=/usr/local/lib" >> ~/.bashrc
source ~/.bashrc
```

#### A serial terminal

On Mac and Linux, you can use the `screen` command to communicate serially with your Blue Pill board. I recommend `miniterm.py`.

##### Mac
```
sudo easy_install pip
pip install pyserial
sudo cp `sudo find /usr -iname miniterm.py` /usr/bin
sudo chmod +x /usr/bin/miniterm.py
```

##### Ubuntu/debian
```
sudo apt-get install python-serial
sudo cp `find /usr -iname miniterm.py` /usr/bin
sudo chmod +x /usr/bin/miniterm.py
```

***
## Build the firmware

#### Get the source code

Download and unzip this repository's zip file or clone it.

```
cd ~
git clone git@github.com:pvico/grbl32.git
```

#### Modify it

Modify as required the CNC machine selection and settings in `grbl/config.h` and `grbl/defaults.h`. The default configuration is for my Ebay/Amazon chinese 3040.

#### Compile it

```
cd ~/grbl32
make
```

***
## Flash the firmware

#### Connect the ST-LINK V2 to the Blue Pill

##### Connection table

| ST-LINK V2 | Blue Pill SWD |
| :--------: | :-----------: |
|  SWCLK     |     SWCLK     |
|  SWDIO     |     SWIO      |
|  GND       |     GND       |
|  3.3V      |     3V3       |

##### Jumper wires at the ST-LINK V2

<img src="/doc/img/STLINK_CONN.jpg">

**Do not use the 5V pin!**

##### Jumper wires at the SWD connector of the Blue Pill

<img src="/doc/img/SWD_CONN.jpg">

##### I made an ad-hoc cable

<img src="/doc/img/ADHOC_CABLE.jpg">

Note: The ST-LINK V2 *does not appear as a serial device* (there is no `/dev/tty*` or `/dev/cu*` file for it).
You can verify the connection with:
```
st-info --probe
```

#### Flash the firmware

```
make flash
```

***
## Serial connection to a PC or other controlling device

### Connection with the USB port of the Blue Pill

#### Check the USB serial device file:

The USB connection will appear as a `/dev/` file device.

##### Mac
```
ls /dev/cu*
```
The device should appear as `/dev/cu.usbmodemxxxxxxxx`.

##### Linux
```
ls /dev/tty*
```
The device should appear as `/dev/ttyUSBx`.

#### Send GRBL commands serially

Connect to it with miniterm at a baud rate of 115200

```
miniterm.py -e /dev/<my_device_file> 115200
```

will return something like this (with your own serial device file that you entered in the previous command).
```
--- Miniterm on /dev/cu.usbserial-A50285BI  921600,8,N,1 ---
--- Quit: Ctrl+] | Menu: Ctrl+T | Help: Ctrl+T followed by Ctrl+H ---
```
Note that you must type Ctrl+] to exit miniterm.

### USART serial connection with a USB to FTDI adapter

To compile the source code for USART connection, comment out the line `-DUSEUSB` just under `CONFIG_DEFS = ...` at the beginning of the Makefile (put a `#` in front of that line).

##### Connection table

| USB<->FTDI | Blue Pill |
| :--------: | :-------: |
|    RX      |    A9     |
|    TX      |    A10    |
|    GND     |    GND    |
|    VCC     |    3.3    |

* Only connect VCC when the Blue Pill is not powered by the ST-LINK or any other device
* Connect VCC to 3.3V of the Blue Pill **only if the jumper on the USB to FTDI adapter is on 3.3V!**
* If the jumper is on 5V or if the adapter has no 3.3V jumper, **connect VCC to 5V of the Blue Pill**. (The Blue Pill has an on-board 3.3V regulator)
* **NEVER CONNECT A PIN OF THE BLUE PILL TO 5V, the only exception is the 5V pin**

##### USB to FTDI adapter SWD connector pinout

<img src="/doc/img/FTDI-PINS.png">

##### Jumper wires at the USB to FTDI adapter

<img src="/doc/img/FTDI_SERIAL.jpg">

Note the jumper on 3.3V.

##### Jumper wires at the Blue Pill

<img src="/doc/img/BP_SERIAL.jpg">

The positive supply is connected to the 3.3 pin. This is OK because the jumper on the FTDI is on 3.3V. If that was not the case, the red wire should be connected to the 5V pin.

##### Simultaneous connection

<img src="/doc/img/STLINK+FTDI.jpg">

Note that the VCC pin of the FTDI is not connected to the Blue Pill as the ST-LINK is powering it.

The USB to FTDI adapter will appear as `/dev/cu.usbserial-xxxxxxxx` on a Mac, on Linux as `/dev/ttyUSBx`.

#### Send GRBL commands serially

```
miniterm.py -e /dev/<my_device_file> 921600
```

Note: if you get garbage or no reply from the blue pill, verify the connection between the blue pill and the USB to FTDI adapter. It is also possible that the baud rate must be reduced. Try a more conservative standard baud rate like 115200. In `inc/config.h`, comment out the line `#define BAUD_RATE 921600` and un-comment `#define BAUD_RATE 115200`. Compile and flash again. Then connect with `miniterm.py -e /dev/<my_device_file> 115200`.  

***
## Test the firmware

Hit the `<return>` key a few times to see if GRBL reacts:
```

ok
ok

ok
ok
```

Hit the `?` key to see the status report, no need for `<return>`
```
?<Alarm|MPos:0.000,0.000,0.000|Bf:199,254|FS:0,0|WCO:0.000,0.000,0.000>
```
On power up, GRBL starts in Alarm state.

Type `$$` + `<return>` to see the GRBL parameters.
```
$0=4
$1=25
$2=0
$3=4
$4=0
$5=0
$6=0
$10=3
$11=0.010
$12=0.002
$13=0
$20=0
$21=1
$22=1
$23=1
$24=25.000
$25=500.000
$26=250
$27=1.000
$30=8000
$31=0
$32=0
$100=800.000
$101=800.000
$102=400.000
$110=3000.000
$111=3000.000
$112=500.000
$120=50.000
$121=50.000
$122=10.000
$130=280.000
$131=377.000
$132=53.000
```
NOTE: if you re-flash a new firmware with modified defaults in `grbl/defaults.h`, the new default parameters will only appear after you reset them. Type `$RST=$` + `<return>` to reset the parameters to the new firmware defaults.

To leave the Alarm state, unlock the device with `$X` followed by `<return>` then hit `?`
```
?<Idle|MPos:0.000,0.000,0.000|Bf:199,254|FS:0,0|Ov:100,100,100|A:S>
```
Type the following command + `<return>`
```
G90
```
GRBL is now in absolute mode, any movement coordinates will be relative to the machine zero position.
Type `X100` + `<return>` and immediately after this, hit the `?` key a few times. You will see the progress.
```
?<Run|MPos:13.664,0.000,0.000|Bf:34,254|FS:2460,0>
?<Run|MPos:31.818,0.000,0.000|Bf:34,254|FS:3000,0>
?<Run|MPos:50.086,0.000,0.000|Bf:34,254|FS:3000,0>
?<Run|MPos:67.565,0.000,0.000|Bf:34,254|FS:3000,0>
?<Run|MPos:83.438,0.000,0.000|Bf:34,254|FS:2190,0>
?<Run|MPos:95.566,0.000,0.000|Bf:34,254|FS:1020,0|WCO:0.000,0.000,0.000>
?<Idle|MPos:100.000,0.000,0.000|Bf:35,254|FS:0,0|Ov:100,100,100>
```

If you connect a [logic analyzer](https://www.saleae.com) or an oscilloscope to the STEP-X pin (normally pin A0 of the blue pill) you can see the step pulses sent to the stepper motor driver.

***
## Connection to the CNC machine

#### Blue Pill pinout (only valid for the 3 axis version)

<img src="/doc/img/BLUE_PILL_PINS.png">

#### Minimum connections to control a basic CNC like the 3040 with a stepper driver board like the CNC shield

* GND
* Positive supply (see below), from either the USB connector, 5V on the 5V pin or 3.3V on the 3.3V pin
* STEPPERS_DISABLE (to the ENABLE pin of the CNC shield)
* X_STEP
* Y_STEP
* Z_STEP
* X_DIRECTION
* Y_DIRECTION
* Z_DIRECTION

All these control pins are output pins from the Blue Pill to the CNC shield. There is no need for a 3.3V <-> 5V conversion.

#### Connection to the device sending the serial commands from the controlling computer or device

TX1 and RX1 must be connected to the device sending the serial commands like the USB to FTDI adapter we used earlier. TX1 must be connected to the RX pin of that device and RX1 to the TX pin. These pins are 3.3V and you must use a 5V <-> 3.3V adapter board if you intend to connect them to a 5V device like an Arduino.

The GND of that device must be connected to GND of the Blue Pill.

#### Power supply connection

The Blue Pill must be powered (positive supply connection) _from only one device_, either the stepper driver board, the serial device or a 3.3V or 5V power supply. Again, do not put 5V on the 3.3 pin!

The stepper driver board will likely take a higher voltage like 12V or 24V to feed the motors but for boards like the [CNC shields](https://www.banggood.com/CNC-Shield-4-X-DRV8825-Driver-Kit-For-Arduino-3D-Printer-p-1134939.html?cur_warehouse=CN), no 3.3V or 5V is derived from this higher voltage. The ubiquitous [Pololu-like](https://www.pololu.com/category/154/drv8825-stepper-motor-driver-carriers-high-current) driver boards (A4988, DRV8825, etc.) do not require a 5V or 3.3V power supply but they do receive 3.3V or 5V input signals from the controlling device (the Blue Pill).

NOTE: the Blue Pill is not pin compatible with the Arduino Nano. **Do not plug it in a board designed for the Arduino Nano** like the [CNC shield V4](https://www.instructables.com/id/How-to-Use-the-CNC-V4-Board-despite-Its-quirks/).

***
## First measurements

We are probing the X_STEP_BIT pin with a logic analyzer after the command `G1 X100 F600` (move 100mm along the X axis with a feed rate of 600mm/min) is issued. The machine parameters are 4mm/revolution, micro-stepping 1/16 and 200 steps/revolution which results in 800 steps/mm. 

The feed rate is 10mm/s so we should have 80,000 steps in about 10" (actually a bit more than 10" considering the acceleration and deceleration phase).

<img src="/doc/img/PROBE1.png">

We see that we have 80,000 pulses in about 10.31" which is correct.

The pulse width is set to 4μs, a conservative value considering that the DRV8825 used as a stepper driver has a minimum pulse width of 1.9μs.

<img src="/doc/img/PROBE2.png">

The pulse widths observed are from 3.95μs to 4.2μs.

If we zoom in on the beginning of the pulse train, we see the acceleration:

<img src="/doc/img/PROBE3.png">

Let's _pretend_ we have a very high performance CNC machine and let's set the X maximum feed rate to 20000mm/min, the X acceleration to 2000 mm/s2 and the pulse width to 2μs

```
$110=20000
ok
ok
$120=2000
ok
ok
$0=2
ok
ok
```

Let's move 2000mm along the X axis at maximum speed

```
G1 F20000 X2000
```

<img src="/doc/img/PROBE4.png">

This gives a frequency of 222kHz, about the maximum we can get with a 3-axis firmware.

**Remember to reset these parameter values or do a `$RST=$` before using the Blue Pill in a real CNC machine.**

***
## Compile for more than 3 axis

In `config.h`, read the instructions in the comments and undefine one of the lines `#define AA_AXIS`, `#define AB_AXIS`, `#define ABC_AXIS`. On top of that file, comment out `#define DEFAULTS_3040T` and un-comment the line `// #define ABC_AXIS_EXAMPLE`

In `defaults.h`, modify as required the parameters for `ABC_AXIS_EXAMPLE` at the end of the file.

For more than 4 axis, only the USB version can be compiled as the B axis step and direction bits use the A9 and A10 pins normally used for USART. Alternatively, the USART can be re-mapped to pins A11 and A12 in the `USART1_Configuration` function in `src/main.c`.

The pin mapping picture above is for the 3 axis version. If you compile with #define ABC_AXIS, you must check all pin assignments in cpu_map.h after #ifdef ABC_AXIS_EXAMPLE (line 404).

***
## Additional documentation

You can find the GRBL commands [in the GRBL wiki](https://github.com/gnea/grbl/wiki/Grbl-v1.1-Commands).

[Here](https://wiki.shapeoko.com/index.php/G-Code) is a list of gcode commands.
