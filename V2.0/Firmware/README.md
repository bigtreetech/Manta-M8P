# Writing bootloader for M8P V2.0
1. Download the [bootloader .bin](./M8P_V2_H723_bootloader.bin) file to CB1/CM4.
2. Press and hold the boot button of M8P and then click the reset button to make M8P goto DFU mode.
3. Run the following command on the Terminal of CB1/CM4 to write the bootloader
    ```
    sudo dfu-util -d ,0483:df11 -R -a 0 -s 0x8000000:leave -D ./M8P_V2_H723_bootloader.bin
    ```
    `0483:df11` is the DFU ID of M8P (We can get it by running `lsusb` command)</br>
    `./M8P_V2_H723_bootloader.bin` is the Path of bootloader file.
