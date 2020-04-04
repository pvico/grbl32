# grbl32
***
From [thomast777/grbl32](https://github.com/thomast777/grbl32). Adapted for command line build on Mac.
Please refer to [gnea/grbl](https://github.com/gnea/grbl) for the core GRBL codes.
***


### Highlights:
* Open in Visual Studio code or Atom for Mac
* 3-axis STM32F103 version only
* Serial on USART1: TX on PB6, RX on PB7
* Serial baud rate of 921600 defined in 'usart.h'
* The STM32F103 [ARM Cortex M3] outputs up to 250 KHz for each axis.
* Can be used on the "Blue Pill" STM32F103C8 board or equivalent circuit


### Prerequisites
* The GNU Embedded Toolchain for Arm

    `brew install gcc-arm-none-eabi`

* The ST-Link software

    `brew install stlink`

* A cheap chinese ST-Link V2 programmer

    [<img src="/docs/ST-LINK V2.jpg">](/docs/ST-LINK V2.jpg)