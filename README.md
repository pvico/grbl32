# grbl32
Refer to [thomast777/grbl32](https://github.com/thomast777/grbl32) for the STM32 code.

Modified for command line building and flashing on Mac/Linux.

Refer to [gnea/grbl](https://github.com/gnea/grbl) for the core GRBL code.

***
## Highlights

* 3-axis STM32F103 version only
* Serial on USART1: TX on PB6, RX on PB7
* Serial baud rate of 921600
* The STM32F103 outputs up to 250 KHz for each axis.
* Can be used on the "Blue Pill" STM32F103C8 board or equivalent circuit

***
## Prerequisites

1. A Blue Pill STM32F103 board

    <img src="/docs/BluePill.jpg">

    Available [here](https://www.amazon.de/dp/B07CRHX5F5/ref=sspa_dk_detail_0?pd_rd_i=B07CRHYTDG&pd_rd_w=BfiZr&pf_rd_p=d3e24f85-c2f2-4959-bef4-3acc5e4e81dc&pd_rd_wg=g8SUT&pf_rd_r=2KXN58GYSKCJW51FNP21&pd_rd_r=665a1ebb-2ed4-4c9e-909b-bb4b300e20a4&spLa=ZW5jcnlwdGVkUXVhbGlmaWVyPUFVWDIyTVpWUkJQTlQmZW5jcnlwdGVkSWQ9QTAzMjQxNjQzRFhMSE9ONUtWMERSJmVuY3J5cHRlZEFkSWQ9QTA1MDE5MjhQR0lLTk5TRU45ODgmd2lkZ2V0TmFtZT1zcF9kZXRhaWwmYWN0aW9uPWNsaWNrUmVkaXJlY3QmZG9Ob3RMb2dDbGljaz10cnVl&th=1).

2. A cheap chinese ST-LINK V2 programmer

    <img src="/docs/ST-LINK V2.jpg">

    Like [this one](https://www.amazon.de/-/en/dp/B07QBLNDPM/ref=sr_1_fkmr1_1?dchild=1&keywords=azdelivery+st-link&qid=1585972853&s=industrial&sr=8-1-fkmr1).

3. A USB to FTDI adapter for testing

    <img src="/docs/FTDI.jpg">

    This one seems to work at 921600 baud. You can get it [here](https://www.amazon.de/dp/B01N9RZK6I/ref=sspa_dk_detail_2?psc=1&pd_rd_i=B01N9RZK6I&pd_rd_w=EiSKu&pf_rd_p=d3e24f85-c2f2-4959-bef4-3acc5e4e81dc&pd_rd_wg=LoeX3&pf_rd_r=ZZ9HJ0736BW5XP9AV9Y1&pd_rd_r=310718f1-2cd9-4810-8e25-4d5afcdf4522&spLa=ZW5jcnlwdGVkUXVhbGlmaWVyPUExVElTVFM0UU1ENTlQJmVuY3J5cHRlZElkPUEwNzY0MTU4MkdRRlZPU0tOWkRURCZlbmNyeXB0ZWRBZElkPUEwNzEyNDIxVkZZREQ5TjVDWkpYJndpZGdldE5hbWU9c3BfZGV0YWlsJmFjdGlvbj1jbGlja1JlZGlyZWN0JmRvTm90TG9nQ2xpY2s9dHJ1ZQ==).

4. The GNU Embedded Toolchain for Arm

    #### Mac
    Note: homebrew must be installed

    ```
    brew install gcc-arm-none-eabi
    ```

    #### Linux

    Install the toolchain as per your Linux distro instructions. For example on debian/ubuntu:
    ```
    sudo apt-get update
    sudo apt-get install gcc-arm-none-eabi
    ```

5. The ST-Link software

    #### Mac

    ```
    brew install stlink
    ```

    #### Linux

    ##### With Ubuntu 19.04 or newer:
    ```
    sudo apt-get install stlink-tools
    ```
    ##### On older debian/ubuntu distros:

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

***
## Build the firmware

#### Get the source code

Download and extract this repository's zip file or clone it

```
cd ~
git clone git@github.com:pvico/grbl32.git
```

#### Modify it

Verify the CNC machine selection and settings in `grbl/config.h` and `grbl/defaults.h`. The default configuration is for my Ebay/Amazon chinese 3040.

#### Compile it

```
cd ~/grbl32
make
```

***
## Flash

#### Connect the ST-LINK V2 to the blue pill

##### Connection table

| ST-LINK V2 | Blue Pill SWD |
| :--------: | :-----------: |
|  SWCLK     |     SWCLK     |
|  SWDIO     |     SWIO      |
|  GND       |     GND       |
|  3.3V      |     3V3       |

##### Jumper wires at the ST-LINK V2

<img src="/docs/STLINK_CONN.jpg">

**Do not use the 5V pin!**

##### Jumper wires at the SWD connector of the Blue Pill

<img src="/docs/SWD_CONN.jpg">

##### I made an ad-hoc cable

<img src="/docs/ADHOC_CABLE.jpg">

Note: The ST-LINK V2 *does not appear as a serial device* (there is no `/dev/tty*` or `/dev/cu*` entry).
You can verify the connection with:
```
st-info --probe
```

#### Flash the firmware

```
make flash
```

***
## Test the firmware

On Mac and Linux, you can use the `screen` command to communicate serially with your blue pill board. I recommend `miniterm.py`.

#### Install miniterm.py

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

#### Connect the USB to FTDI adapter to the Blue Pill

##### Connection table

| USB<->FTDI | Blue Pill |
| :--------: | :-------: |
|    RX      |    B6     |
|    TX      |    B7     |
|    GND     |    GND    |
|    VCC     |    3.3    |

* Only connect VCC if the Blue Pill is not powered by the ST-LINK
* Connect VCC to 3.3V of the Blue Pill **only if the jumper on the USB to FTDI adapter is on 3.3V!**
* If the jumper is on 5V or if the adapter has no 3.3V jumper, **connect VCC to 5V of the Blue Pill**. (The Blue Pill has an on-board 3.3V regulator)
* **NEVER CONNECT A PIN OF THE BLUE PILL TO 5V, the only exception is the 5V pin**

##### USB to FTDI adapter pinout

<img src="/docs/FTDI-PINS.png">

##### Jumper wires at the USB to FTDI adapter

<img src="/docs/FTDI_SERIAL.jpg">

Note the jumper on 3.3V

##### Jumper wires at the Blue Pill

<img src="/docs/BP_SERIAL.jpg">

The positive supply is connected to the 3.3 pin. This is acceptable because the jumper on the FTDI is on 3.3V. If that was not the case, the red wire should be connected to the 5V pin

##### Simultaneous connection

<img src="/docs/STLINK+FTDI.jpg">

Note that the VCC pin of the FTDI is not connected to the Blue Pill, the ST-LINK is powering it

#### Check the FTDI device address:

##### Mac
The device should appear as `/dev/cu.usbserial-xxxxxxxx`
```
ls /dev/cu*
```

##### Linux
The device should appear as `/dev/ttyUSBx`
```
ls /dev/tty*
```

#### Send GRBL commands serially

```
miniterm.py -e /dev/<my_device_address> 921600
```
will return something like this (with your own device serial address that you entered in the previous command)
```
--- Miniterm on /dev/cu.usbserial-A50285BI  921600,8,N,1 ---
--- Quit: Ctrl+] | Menu: Ctrl+T | Help: Ctrl+T followed by Ctrl+H ---
```
Note that you must type Ctrl+] to exit miniterm

Note: if you get garbage or no reply from the blue pill, verify the connection between the blue pill and the USB to FTDI adapter. It is also possible that the baud rate must be reduced. Try a more conservative standard baud rate like 115200. In `Src/usart.h`, comment out the line `#define USART_BAUD_RATE 921600` and un-comment `#define USART_BAUD_RATE 115200`. Compile and flash again. Then connect with `miniterm.py -e /dev/my_device_address 115200`.  

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
On power up, GRBL starts in Alarm state

Type `$$` + `<return>` to see the GRBL parameters
```
$0=0.500
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
$20=1
$21=0
$22=1
$23=1
$24=25.000
$25=500.000
$26=250
$27=1.000
$30=5000
$31=0
$32=0
$40=10000.000
$50=0
$100=800.000
$101=800.000
$102=400.000
$110=3000.000
$111=3000.000
$112=500.000
$120=10.000
$121=10.000
$122=10.000
$130=280.000
$131=377.000
$132=53.000
```
NOTE: if you re-flashed a new firmware with modified defaults in `grbl/defaults.h`, the new default parameters will only appear after you reset them. Type `$RST=$` + `<return>` to reset the parameters to firmware defaults.

To leave the Alarm state, unlock the device with `$X` followed by `<return>`. then hit `?`
```
?<Idle|MPos:0.000,0.000,0.000|Bf:199,254|FS:0,0|Ov:100,100,100|A:S>
```
Type the following command + `<return>`
```
G90 G94 G21 F100
```
GRBL is now in absolute mode, any move coordinates will be relative to the machine zero position.
Type `X100` + `<return>` and immediately after hit the `?` key a few times. You will see the move progress
```
?<Run|MPos:5.665,0.000,0.000|Bf:198,254|FS:642,0>
?<Run|MPos:14.036,0.000,0.000|Bf:198,254|FS:1009,0>
?<Run|MPos:26.131,0.000,0.000|Bf:198,254|FS:1375,0>
?<Run|MPos:43.985,0.000,0.000|Bf:198,254|FS:1783,0>
?<Run|MPos:63.030,0.000,0.000|Bf:198,254|FS:1628,0|WCO:0.000,0.000,0.000>
?<Run|MPos:78.075,0.000,0.000|Bf:198,254|FS:1253,0|Ov:100,100,100|A:S>
?<Run|MPos:89.926,0.000,0.000|Bf:198,254|FS:848,0>
?<Run|MPos:97.468,0.000,0.000|Bf:198,254|FS:420,0>
?<Idle|MPos:100.000,0.000,0.000|Bf:199,254|FS:0,0>
```

If you connect a [logic analyser](https://www.saleae.com) or an oscilloscope to the STEP-X pin (normally pin A0 of the blue pill) you can see the step pulses sent to the stepper motor driver.

You can find the GRBL commands [in the GRBL wiki](https://github.com/gnea/grbl/wiki/Grbl-v1.1-Commands).

You can find a list of gcode commands [here](https://wiki.shapeoko.com/index.php/G-Code)