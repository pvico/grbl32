# grbl32
***
From [thomast777/grbl32](https://github.com/thomast777/grbl32). Adapted for command line build on Mac/Linux.
Please refer to [gnea/grbl](https://github.com/gnea/grbl) for the core GRBL codes.
***


### Highlights

* 3-axis STM32F103 version only
* Serial on USART1: TX on PB6, RX on PB7
* Serial baud rate of 921600 defined in 'usart.h'
* The STM32F103 [ARM Cortex M3] outputs up to 250 KHz for each axis.
* Can be used on the "Blue Pill" STM32F103C8 board or equivalent circuit


### Prerequisites
* A cheap chinese ST-LINK V2 programmer

    <img src="/docs/ST-LINK V2.jpg">

    Like [this one](https://www.amazon.de/-/en/dp/B07QBLNDPM/ref=sr_1_fkmr1_1?dchild=1&keywords=azdelivery+st-link&qid=1585972853&s=industrial&sr=8-1-fkmr1).

* A USB to FTDI adapter for testing

    <img src="/docs/FTDI.jpg">

    This one seem to work at 921600 baud. You can get it [here](https://www.amazon.de/dp/B01N9RZK6I/ref=sspa_dk_detail_2?psc=1&pd_rd_i=B01N9RZK6I&pd_rd_w=EiSKu&pf_rd_p=d3e24f85-c2f2-4959-bef4-3acc5e4e81dc&pd_rd_wg=LoeX3&pf_rd_r=ZZ9HJ0736BW5XP9AV9Y1&pd_rd_r=310718f1-2cd9-4810-8e25-4d5afcdf4522&spLa=ZW5jcnlwdGVkUXVhbGlmaWVyPUExVElTVFM0UU1ENTlQJmVuY3J5cHRlZElkPUEwNzY0MTU4MkdRRlZPU0tOWkRURCZlbmNyeXB0ZWRBZElkPUEwNzEyNDIxVkZZREQ5TjVDWkpYJndpZGdldE5hbWU9c3BfZGV0YWlsJmFjdGlvbj1jbGlja1JlZGlyZWN0JmRvTm90TG9nQ2xpY2s9dHJ1ZQ==).

* The GNU Embedded Toolchain for Arm

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

* The ST-Link software

    #### Mac

    ```
    brew install stlink
    ```

    #### Linux

    ##### With Ubuntu 19.04 or newer:
    ```
    sudo apt-get install stlink-tools
    ```
    ##### On an older debian/ubuntu:

    ```
    sudo apt-get install libusb-1.0-0-dev git cmake build-essential
    git clone https://github.com/texane/stlink stlink.git
    cd stlink.git
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


### Build

Verify the defults settings in `grbl/config.h` and `grbl/defaults.h`. The default configuration is for my Ebay/Amazon chinese 3040.

```
make
```


### Flash the firmware

