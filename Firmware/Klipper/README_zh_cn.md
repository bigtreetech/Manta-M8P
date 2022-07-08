# [View English version](./README.md)

# 在 Manta M8P 主板上使用 Klipper

## 注意: 

* 此主板出厂时自带了引导程序，允许通过 MCU SD 卡更新固件(firmware.bin)。

## 编译固件
1. 预编译的固件(预编译的固件源码版本是 [Commits on Jul 1, 2022](https://github.com/Klipper3d/klipper/commit/1636a9759bc2d5f162312ac8bf5823e95e0ad053))
   * [firmware-USB.bin](./firmware-USB.bin) 使用 USB 与树莓派通信。M8P 上的 MCU 的 USB 在板上与 CB1/CM4 已经连接好了，更新完固件即可使用，不需要额外的接线。

2. 自行编译最新版本的固件<br/>
   1. 参考 [klipper官方的安装说明](https://www.klipper3d.org/Installation.html) 下载klipper源码到树莓派
   2. 使用下面的配置去编译固件 (如果您的klipper无法选择如下的配置，请更新您的klipper源码)
      * [*] Enable extra low-level configuration options
      * Micro-controller Architecture = `STMicroelectronics STM32`
      * Processor model = `STM32G0B1`
      * Bootloader offset = `8KiB bootloader`
      * Clock Reference = `8 MHz crystal`
      * Communication interface = `USB (on PA11/PA12)`

      <img src=Images/menuconfig.png width="800" /><br/>
   3. 配置选择完成后, 输入 `q` 退出配置界面，当询问是否保存配置是选择 "Yes" .
   4. 输入 `make` 命令开始编译固件
   5. 当 `make` 命令执行完成后，会在树莓派的`home/pi/kliiper/out`的文件夹中生成我们所需要的`klipper.bin`固件。你可以在CMD命令行终端中通过`pscp`命令把`klipper.bin`固件复制到与树莓派在同一个局域网下的电脑上。例如 `pscp -C pi@192.168.0.101:/home/pi/klipper/out/klipper.bin c:\klipper.bin`(命令行会提示 `The server's host key is not cached` 并且询问 `Store key in cache?((y/n)`, 输入 `y` 保存 host key，然后输入树莓派默认的密码：`raspberry`)

## 更新固件
1. 你可以使用 [编译固件 2.5](#编译固件) 中的方法或者使用 `cyberduck`、 `winscp` 工具软件从树莓派中将 `klipper.bin` 文件复制到电脑上
2. 将我们提供的`firmware-USB.bin`, 或者你自行编译的 `klipper.bin` 文件重命名为 `firmware.bin`<br/>
**提示:** 如果没有重命名为 `firmware.bin`，引导程序将不会识别并更新此文件
3. 复制 `firmware.bin` 到MCU SD卡的根目录中(确保SD卡的文件系统是FAT32格式)
4. 将主板断电
5. 插入MCU SD卡
6. 给主板通电
7. 仅需几秒钟，主板就会自动完成更新固件的步骤
8. 你可以输入 `ls /dev/serial/by-id` 查询主板的串口ID来确认固件是否烧录成功，如果烧录成功了会返回一个klipper的设备ID，如下图所示:

   <img src=Images/stm32g0b1_id.png width="600" /><br/>

   (注意: 此步骤仅适用于USB通信的工作方式，如果使用USART通信则没有这种ID)

## 配置打印机的参数
### 基础配置
1. 参考 [klipper官方的安装说明](https://www.klipper3d.org/Installation.html) to `Configuring OctoPrint to use Klipper`
2. 参考 [klipper官方的安装说明](https://www.klipper3d.org/Installation.html) to `Configuring Klipper`. 并且使用我们提供的配置文件 [generic-bigtreetech-manta-m8p.cfg](./generic-bigtreetech-manta-m8p.cfg) 为基础去修改 `printer.cfg`, 此文件中包含了主板几乎所有的pinout
3. 参考 [klipper官方的配置说明Config_Reference](https://www.klipper3d.org/Config_Reference.html) 去配置你想要的特性和功能
4. 如果你想通过USB与树莓派通信，运行 `ls /dev/serial/by-id/*` 命令去查询主板的设备ID号，在 `printer.cfg` 设置查询到的实际设备ID号
    ```
    [mcu]
    serial: /dev/serial/by-id/usb-Klipper_stm32g0b1xx_190028000D50415833323520-if00
    ```
