# [切换到中文版](./README_zh_cn.md)

# How to use Klipper on Manta M8P

## NOTE: 

* This motherboard comes with bootloader which allows firmware update through MCU SD card.

## Build Firmware Image

1. Precompiled firmware(The source code version used is [Commits on Jul 1, 2022](https://github.com/Klipper3d/klipper/commit/1636a9759bc2d5f162312ac8bf5823e95e0ad053))
   * [firmware-USB.bin](./firmware-USB.bin) Use USB to communicate with SoC. The USB of MCU on M8P has been connected with CB1/CM4 on the board. After updating the firmware, it can be used without additional wiring.

2. Build your own firmware<br/>
   1. Refer to [klipper's official installation](https://www.klipper3d.org/Installation.html) to download klipper source code to raspberry pi.
   2. `Building the micro-controller` with the configuration shown below. (If your klipper cannot select the following configuration, please update your klipper source code)
      * [*] Enable extra low-level configuration options
      * Micro-controller Architecture = `STMicroelectronics STM32`
      * Processor model = `STM32G0B1`
      * Bootloader offset = `8KiB bootloader`
      * Clock Reference = `8 MHz crystal`
      * Communication interface = `USB (on PA11/PA12)`
      * If you plan to use the USB to CAN bridge (only with M8P V1.1+) then use the following two lines instead of the above line:
      * Communication interface = `USB to CAN bus bridge (USB (on PA11/PA12))`
      * CAN bus interface = `(CAN bus (on PD12/PD13))`

      <img src=Images/menuconfig.png width="800" /><br/>
   3. Once the configuration is selected, press `q` to exit,  and "Yes" when  asked to save the configuration.
   4. Run the command `make`
   5. The `klipper.bin` file will be generated in the folder `home/pi/kliiper/out` when the `make` command completed. And you can use the windows computer under the same LAN as raspberry pi to copy `klipper.bin` from raspberry pi to the computer with `pscp` command in the CMD terminal. such as `pscp -C pi@192.168.0.101:/home/pi/klipper/out/klipper.bin c:\klipper.bin`(The terminal may prompt that `The server's host key is not cached` and ask `Store key in cache?((y/n)`, Please type `y` to store. And then it will ask for a password, please type the default password `raspberry` for raspberry pi)

## Firmware Installation
1. You can use the method in [Build Firmware Image 2.5](#build-firmware-image) or use a tool such as `cyberduck` or `winscp` to copy the `klipper.bin` file from your pi to your computer.
2. Renamed the `firmware-USB.bin` or `klipper.bin`(in folder `home/pi/kliiper/out` build by yourself) to `firmware.bin`<br/>
**Important:** If the file is not renamed, the bootloader will not be updated properly.
3. Copy the `firmware.bin` to the root directory of MCU SD card (make sure SD card is in FAT32 format)
4. power off the motherboard
5. insert the MCU SD card
6. power on the motherboard
7. after a few seconds, the motherboard should be flashed
8. you can confirm that the flash was successful, by running `ls /dev/serial/by-id`.  if the flash was successful, this should now show a klipper device, similar to:

   <img src=Images/stm32g0b1_id.png width="600" /><br/>

## Configure the printer parameters
### Basic configuration
1. Refer to [klipper's official installation](https://www.klipper3d.org/Installation.html) to `Configuring OctoPrint to use Klipper`
2. Refer to [klipper's official installation](https://www.klipper3d.org/Installation.html) to `Configuring Klipper`. And use the configuration file [generic-bigtreetech-manta-m8p.cfg](./generic-bigtreetech-manta-m8p.cfg) as the underlying `printer.cfg`, which includes all the correct pinout for Octopus
3. Refer to [klipper's official Config_Reference](https://www.klipper3d.org/Config_Reference.html) to configure the features you want.
4. If you use USB to communicate with raspberry pi, run the `ls /dev/serial/by-id/*` command in raspberry pi to get the correct ID number of the motherboard, and set the correct ID number in `printer.cfg`.
    ```
    [mcu]
    serial: /dev/serial/by-id/usb-Klipper_stm32g0b1xx_190028000D50415833323520-if00
    ```
