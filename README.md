## Starter app for the Maple Mini on ChibiOS

This is the sample demo that was supplied with the ChibiOS demos, broken out into its own directory to be put under source control.

It serves as a minimal application for testing that your tool chain is properly setup, and as an easily cloned starting point for other ChibiOS projects on the maple mini.

### Hardware Setup

The hardware used is:

* Maple mini STM32C103 board.
* ST Link V2 programmer.

Clones of both of these can be found inexpensively on ebay and Amazon.com.

Connect the ST Link and mini maple pins together as follows.

| Signal Name | Maple Mini Pin label | ST Link Pin Label |
| ----------- | -------------------- | ----------------- |
| GND         | GND                  | GND               |
| SWDIO       | 22                   | SWDIO             |
| SWCLK       | 21                   | SWCLK             |

For this setup, power is provided to the maple mini by its own USB connection, so you will connect both the maple mini and the ST Link to your host computer.

### Software Setup (For Ubuntu Linux 16.04)

1. Install Eclipse Neon [from this link](https://eclipse.org/)

2. Install the following packages:
  ```
  sudo add-apt-repository ppa:team-gcc-arm-embedded/ppa
  sudo apt-get update
  sudo apt-get install gcc-arm-none-eabi gdb-arm-none-eabi libnewlib-arm-none-eabi openocd
  ```

3. Set up a symlink required by OpenOCD for Ubuntu:
  ```
  cd /usr/local/share
  sudo ln -s /usr/share/openocd .
  ```

4. Copy Makefile.default to Makefile, and edit it to change the CHIBIOS variable to point to your local copy of the ChibiOS source tree

5. Prior to initiating a debug session, you must start openOCD. use the following command to do that:
   ```
   openocd -f /usr/local/share/openocd/scripts/interface/stlink-v2.cfg -f /usr/local/share/openocd/scripts/target/stm32f1x_stlink.cfg
   ```

6. Follow the instructions at these two links, with the changes listed here:

  [Setting up an Eclipse-based IDE](http://www.chibios.org/dokuwiki/doku.php?id=chibios:guides:eclipse1)

  Changes:
    * The "Usage Data Collector" option is not present in the version of Eclipse that I used

  [Using an Eclipse-based IDE](http://www.chibios.org/dokuwiki/doku.php?id=chibios:guides:eclipse2)

  Changes:
    * In the GDB Hardware Debugging Dialog "Main" tab, change the "C/C++ Application" path to include the complete absolute path to the elf file in your project's build directory.
    * The "GDB Hardware Debugging" option does not appear in the version of eclipse that I used.
    * Instead of the demo directory, use the directory that this project is in.
    * When creating the two linked folders to locations in the ChibiOS tree, the first is to 'os' and the second is to 'os/hal/boards/MAPLEMINI_STM32_F103'
    * When setting up the debug configuration, the message “Using Standard GDB Hardware Debugging Launcher” does not appear in the main tab, don't worry.
    * Ignore all fialog fields that are extra from the ones shown to be configured in the instructions.
    * In the startup tab, use these instructions for the Initialization commands.

    SUBSTITUTE THE PATH TO YOUR OWN PROJECT DIRECTORY.

    ```
    monitor reset halt
    monitor reset halt
    monitor wait_halt
    monitor poll
    monitor flash probe 0
    monitor stm32f1x mass_erase 0
    monitor flash write_bank 0 /src/chibios-maple-mini-basic/build/ch.bin 0
    monitor reset halt
    ```
    